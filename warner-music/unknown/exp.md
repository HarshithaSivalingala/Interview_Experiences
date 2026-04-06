Sharing my experience from 1st round of interview at warner music. Have never seen this version of interview it was different hence sharing here. It was last year

They asked to use AI to complete the tasks. 

Pre read given before hand


I'm passing along pre-read material for your coding round.

Context
WMG distributes music to partners like Spotify via the concept of an Order, which represents an individual song. These Orders are delivered to partners in the form of Metadata (XML) and Assets (audio WAV, coverart TIFF, etc) files.



What You Need to Do

You will build an “Artifact Generation” client that ingests Orders from a queue, orchestrates the production of Metadata and Assets, along with their respective AssetDetails, and publishes the results for further processing. This happens via a series of JSON APIs, implemented in a testserver binary that you will be provided during the interview. These APIs may not always be reliable, and your client must handle failure correctly.


Generally speaking, for each Order, you will:


take an Order from the queue

enqueue asynchronous Asset generation

wait till it finishes, and then get Asset and AssetDetail data

enqueue asynchronous Metadata generation

wait till it finishes, and then get Metadata data

bundle all the data together and submit it as a ShippableOrder

Data Model
The relationship between Orders, Assets, AssetDetails and Metadata is as follows:


An Order has 1:many Assets => An Asset belongs to a single Order

An Order has 1 Metadata => A Metadata belongs to a single Order

An Asset has 1 AssetDetail => An AssetDetail belongs to a single Asset

Orchestration Requirements
Specifically, there are some rules you must follow while processing each Order, as listed below:


You must "take" an Order from the queue before doing anything else with it

You must finish Asset generation before triggering any Metadata generation

You must not trigger Metadata generation if Asset generation failed

If Asset generation fails:

You must submit a FailedOrder without Asset/AssetDetail or Metadata data

If Metadata generation fails:

You must submit a FailedOrder with Asset/AssetDetail data but without Metadata data

If both Asset and Metadata generation succeed:

You must submit a ShippableOrder with both the Asset/AssetDetail data and Metadata data

Tips
There are no dependencies between Orders; the status/processing of one Order does not impact any other Order

If you break the state machine by failing to follow the above orchestration rules, you cannot recover it

If you submit an Order incorrectly (incorrect data/status), you cannot recover it

.
During interview

I was asked to improve the performance of the orchestrator service. 

I was asked to use the AI.

On snippet of the code pasted from the prompt I had



package client;

import java.util.*;
import java.util.stream.Collectors;

import sdk.IngestionAPI;
import sdk.LegacyGenerationAPI;
import sdk.SubmissionAPI;
import sdk.models.Order;
import sdk.models.Asset;
import sdk.models.AssetDetail;
import sdk.models.FullAsset;
import sdk.models.Metadata;
import sdk.models.ShippableOrder;

public class Client {
    public static void main(String[] args) {
        final String baseUrl = "http://127.0.0.1:9200";
        final IngestionAPI ingestion = new IngestionAPI(baseUrl);
        final SubmissionAPI submission = new SubmissionAPI(baseUrl);
        final LegacyGenerationAPI legacy = new LegacyGenerationAPI(baseUrl);
        try {
            System.out.println("start; server status:" + legacy.getStatus());
            // ingestion
            Set<Order> peekedOrders = ingestion.peekOrders(1);
            Order peekedOrder = peekedOrders.iterator().next();
            List<String> references = new ArrayList<>();
            references.add(peekedOrder.referenceNumber());
            Set<Order> takenOrders = ingestion.takeOrders(references);
            Order order = takenOrders.iterator().next();
            // asset generation
            legacy.queueGenerateAssets(order.referenceNumber());
            Set<Asset> assets = legacy.getAssets(order.referenceNumber());
            if (assets.isEmpty()) {
              throw new RuntimeException("did asset generation complete? did it complete successfully?");
            }
            Set<Integer> assetIds = assets.stream()
                    .map(Asset::assetId)
                    .collect(Collectors.toSet());
            Set<AssetDetail> assetDetails = legacy.getAssetsDetails(assetIds);
            Map<Integer, AssetDetail> detailsMap = assetDetails.stream()
                    .collect(Collectors.toMap(AssetDetail::assetId, detail -> detail));
            List<FullAsset> fullAssets = new ArrayList<>();
            for (Asset asset : assets) {
                AssetDetail detail = detailsMap.get(asset.assetId());
                if (detail != null) {
                    fullAssets.add(new FullAsset(asset, detail));
                }
            }
            // metadata generation
            legacy.queueGenerateMetadata(order.referenceNumber());
            String metadataStatus = legacy.getMetadataStatus(order.referenceNumber());
            if (!metadataStatus.equals("SUCCESS")) {
              throw new RuntimeException("did metadata generation complete? did it complete successfully?");
            }
            Set<String> orderReferences = new HashSet<>();
            orderReferences.add(order.referenceNumber());
            Set<Metadata> metadatas = legacy.getMetadata(orderReferences);
            Metadata metadata = metadatas.iterator().next();
            // submission
            submission.submitShippableOrder(new ShippableOrder(order, fullAssets, metadata));
            System.out.println("shippable order submitted; server status:" + legacy.getStatus());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
