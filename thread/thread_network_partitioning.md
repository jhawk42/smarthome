# Thread Network Partitioning

Source: Google Gemini

A Thread partition is a group of Thread nodes that share a common Leader,
Partition ID, and Network Data. Under normal conditions, all nodes in a
Thread network belong to a single partition. Partitioning occurs when a
subset of nodes can no longer reach the rest of the network.

## What Causes a Partition:
- RF interference    : signal degradation or sustained noise severs the
                    radio path between two groups of routers.
- Physical obstruction: walls, floors, or moved equipment block the
                    2.4 GHz path between router nodes.
- Router failure     : a key intermediate router loses power or crashes,
                    splitting the topology into disconnected islands.
- Network growth     : a new deployment zone (e.g., remote outbuilding)
                    without overlapping router coverage naturally forms
                    a separate partition until a bridge router is added.
- Leader loss + RF gap: if the Leader goes offline and the two halves of
                    the network cannot hear each other's MLE Advertisements,
                    each half independently elects a new Leader and
                    forms its own partition.

# How Partitioning Happens (Step by Step):

1. Link failure detection.
    Routers monitor neighbor reachability via periodic MLE Advertisement
    messages (sent every ~30-60 s). If a router misses several consecutive
    Advertisements from a neighbor, it marks that link as failed and
    removes it from its routing table.

2. Topology re-convergence attempt.
    The affected routers attempt to re-route traffic through alternate
    mesh paths. If alternate paths exist, traffic continues and no
    partition occurs -- the mesh is self-healing.

3. Partition formation.
    If no alternate path exists, nodes on each side of the break stop
    receiving the Leader's MLE Advertisements. Each isolated group:
    a. Waits for Leader Advertisements to time out.
    b. Runs the Leader Election process (random backoff, weight
        comparison -- see section 2.9).
    c. The winner declares itself Leader, generates a new 32-bit
        Partition ID, and begins distributing Network Data.
    Both groups now operate as independent, fully functional Thread
    networks with different Partition IDs.

4. Impact on devices.
    Devices within each partition continue to operate normally.
    Cross-partition communication is not possible until the network
    re-merges. RLOC16 addresses are partition-local and may collide
    between partitions.

## Detecting a Partition:

CLI indicators:
- ot-ctl partitionid         -- shows the local Partition ID; compare
                                across suspected isolated nodes to
                                confirm they differ.
- ot-ctl leaderdata          -- Partition ID field; mismatched values
                                confirm separate partitions.
- ot-ctl state               -- a former child that is now 'leader' on
                                a small island is a strong indicator.
- ot-ctl router table        -- a reduced router count compared to
                                expected topology suggests a split.
- ot-ctl netdata show        -- missing Border Router prefixes indicate
                                the device cannot reach its normal BR.
## Log / counter indicators:

Rising attachAttemptsCount (TLV 34) on end devices attempting to
find a stable parent across the partition boundary.
Frequent Partition ID changes in leaderdata output.

## Partition Merge (Healing):

When the RF path or failed router is restored, partitions that can hear
each other merge automatically. The merge algorithm (also described in
section 2.9) compares:
1. Leader Weight   -- higher weight wins.
2. Router count    -- more routers wins (if weights equal).
3. Partition ID    -- higher value wins (if still tied).
The losing partition's routers re-attach to the winning partition's
Leader. Router IDs are reassigned as needed, causing RLOC16 churn for
affected nodes. End devices transparently re-parent to the merged mesh.

## Best Practices to Prevent Partitioning:
- Deploy mains-powered router-capable devices (smart plugs, bulbs)
densely enough that each router has at least two 3-link neighbors.
- Ensure every physical area is covered by at least two routers so
a single failure never isolates a zone.
- Assign higher leaderweight values to always-on Border Routers to
keep the Leader on a stable, central node.
- Monitor router count and neighbor link quality regularly; investigate
any router with zero 3-link neighbors.
- Keep Thread channels clear of Wi-Fi interference (see section 2.6).
