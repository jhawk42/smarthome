# Thread Network Troubleshooting

Source: Google Gemini

## 1. Link Quality (LQI)

Thread link quality is measured 0-3 based on bidirectional RSSI.

| LQI | Quality        |  Description                    |  Action                   |
| --- | -------------- | ------------------------------- | ------------------------- |
|  3  | Excellent      | Very high signal, minimal noise | None required             |
|  2  | Good/Stable    | Solid; may reroute occasionally | None; acceptable baseline |
|  1  | Poor/Weak      | Marginal; interference or range | Add powered router nodes  |
|  0  | Unknown/None   | No direct link or disconnected  | Investigate immediately   |

Notes:
- Some devices report LQI 2-3 even at excellent RSSI; the value can
  reflect path stability rather than raw signal strength.
- Thread uses 2.4 GHz; set Wi-Fi to non-overlapping channels (e.g., Wi-Fi
  channel 1) so Thread can use channels 15, 20, or 25 interference-free.
- Keep Border Routers (HomePod Mini, Nest Hub) away from metal objects.


## 2. Device Distance & Placement

- Ideal point-to-point range   : up to 30 m (~100 ft) in open space
- Practical indoor range       : ~10 m (~33 ft) with walls and obstructions
- Recommended BR-to-end-device : within 25-30 ft (8-10 m) for stable links

Extending range:
- Add mains-powered Thread routers (smart plugs, smart bulbs) between
  a Border Router and distant end devices.
- Place Border Routers centrally, 1-2 m off the ground, away from Wi-Fi
  access points and metal appliances.
- USB 3.0 devices generate broadband RF noise -- keep them clear of BRs.

## 3. MAC Counters

Ref: https://openthread.io/reference/struct/ot-mac-counters

otMacCounters tracks low-level IEEE 802.15.4 frame statistics.


| Counter                 | Description                        | Common Cause         |
| ----------------------- | ---------------------------------- | ---------------------|
| mRxErrFcs               | Bad Frame Check Sequence           | RF interference      |
| mRxErrSec               | Security failure (key/counter)     | Key mismatch         |
| mRxDuplicated           | Duplicate frame (ACK failure)      | Sender retrying      |
| mRxDestAddrFiltered     | Frame for another node (discarded) | Normal in dense mesh |
| mRxErrInvalidSrcAddr    | Unrecognized source address        | Stale neighbor entry |
| mRxErrNoFrame           | Malformed/empty frame              | RF corruption        |

Troubleshooting steps:
- High mRxErrFcs    : check for RF interference, physical obstructions, or
                    USB 3.0 devices near the Border Router.
- High mRxErrSec    : verify network keys are consistent across all devices.
- High mRxDuplicated: nodes are struggling to ACK; check link quality / range.
- High discards     : device may be overloaded or out of buffer; reduce TX rate
                      or reboot the device to clear hung buffers.

General guidance:
- Monitor counters over time; rising trends require investigation.
- Minor discards are normal in low-power wireless networks.
- Reboot unresponsive accessories (power cycle) to clear hung state.

Errors vs Discards (key distinction from implementation experience):
- Errors  : caused by malformed or corrupted packets -- RF interference, weak
        signal, or physical obstructions that corrupt the frame in transit.
        High errors relative to total packets indicate a signal quality
        problem.
- Discards: caused by resource exhaustion -- the device ran out of buffer
        space or was overloaded and dropped packets it could not process.
        High discards indicate congestion or insufficient buffer capacity,
        not necessarily a radio problem.
- Tip: calculate each counter as a ratio of total TX/RX packets to
determine whether high absolute counts are actually significant.

## 4. MLE Counters

MLE (Mesh Link Establishment) counters are reported via TLV 34.

- attachAttemptsCount: 
  Tracks how many times a child/end device has attempted to attach to a
  parent router. Uses exponential backoff to protect battery on repeated
  failures.
  High value indicates: device too far from a router, or RF interference
  is preventing a stable connection.
  Fix: add more router-capable mains-powered devices near the failing device.

- betterPartIdAttachAttemptsCount: 
  Subset of total attach attempts; counts attempts to switch to a "better"
  parent (better Partition ID or higher link quality).
  High or rapidly rising value indicates: the child cannot find a stable
  parent, or network topology is frequently changing.
  Fix: review Border Router placement and network layout.

Notes: Related counters in otMleCounters / otNetworkDiagMleCounters:
  mAttachAttempts, parent responses, child updates.

