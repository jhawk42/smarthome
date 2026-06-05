# Thread Leader Election

Source: Google Gemini

Every Thread partition has exactly one Leader at all times. The Leader is
always a Router (FTD) and is responsible for:
- Assigning and reclaiming Router IDs (via MLE Router ID Request/Response)
- Maintaining and distributing the Thread Network Data to all routers
- Managing active Router count: promoting REEDs when count drops below the minimum threshold, and downgrading Routers to REEDs when it exceeds the maximum threshold.

## OpenThread default router count thresholds:

- Minimum active routers : 16  (promotes REEDs to fill gaps)
- Maximum active routers : 32  (downgrades Routers to REEDs to cap growth)

## Leader Data TLV:

  Every Router continuously advertises Leader Data in its MLE Advertisement:
    Partition ID       : 32-bit random value identifying this partition.
    Weighting          : 0-255; higher value = stronger claim to leadership.
    Data Version       : incremented on any Network Data change.
    Stable Data Version: incremented on stable Network Data changes only.
    Leader Router ID   : Router ID of the current Leader.

## Leader Weight:

  The primary election criterion. Configured per device via:
    ot-ctl leaderweight <0-255>
  Default: 64. Best practice: assign higher weights (e.g., 255) to
  mains-powered, always-on devices such as Border Routers so they
  naturally become and remain Leader. Avoid battery-powered devices
  acquiring the Leader role -- a sleeping Leader causes network disruption.

## Initial Network Formation:

  The first Router to form a new Thread partition automatically becomes
  the Leader. It self-assigns a random Partition ID and begins advertising
  Leader Data immediately.

## Re-election on Leader Loss:

Routers detect Leader absence through missing MLE Advertisement messages.
The re-election sequence:
  1. Each Router independently starts a random backoff timer (jitter
      prevents simultaneous elections).
  2. The Router whose timer expires first declares itself Leader,
      generates a new Partition ID, and starts advertising.
  3. Remaining Routers receive the new Leader Data, synchronize their
      Network Data, and re-register their Router IDs with the new Leader.
Rapid or repeated leader changes indicate underlying RF or topology
instability and should be investigated.


## Partition Merge (Split-brain Recovery):

Network splits create isolated partitions, each with its own Leader.
When partitions reconnect, a merge takes place:
1. Leader Weight comparison  : the partition with the higher Leader
    Weight wins and retains its Leader.
2. Router count tiebreaker   : if weights are equal, the partition
    with more active Routers wins.
3. Partition ID tiebreaker   : if still equal, the higher Partition ID
    wins.
Routers in the losing partition re-attach to the winning partition's
Leader. Router IDs may be re-assigned during this process, causing
RLOC16 changes for affected nodes.


## CLI commands:

- ot-ctl leaderdata         -- show current Leader Data (weight, partition ID,
                               data versions, leader router ID)
- ot-ctl leaderweight       -- get configured leader weight for this device
- ot-ctl leaderweight <val> -- set leader weight (0-255)
- ot-ctl state              -- shows current role: leader / router / child / detached

  Example (leaderdata output):
    Partition ID: 0x4f41cc25
    Weighting: 64
    Data Version: 35
    Stable Data Version: 33
    Leader Router ID: 0x3a
    Done
