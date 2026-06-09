# Thread Parent Router Selection

Source: Google Gemini

A Thread node (End Device or REED) selects a parent router through a
structured Mesh Link Establishment (MLE) attach process. The goal is to find
the parent offering the highest bidirectional reliability, not simply the
strongest raw signal.

## Step 1 -- Active Discovery (Parent Request):

  The joining or re-attaching node sends a multicast MLE Parent Request to
  all neighboring routers and REEDs within radio range. Each eligible neighbor
  replies with a Parent Response containing:
    - Its current role (Router or REED)
    - Bidirectional link quality / link margin
    - Available child table capacity
    - Network connectivity information

## Step 2 -- Selection Criteria:

  The joining node scores each Parent Response on:
    Link quality (RSSI / link margin)  : primary criterion; bidirectional
                                         reliability is weighted most heavily.
    Child table capacity               : parent must have a free child slot
                                         (theoretical max 511; practical max
                                         much lower).
    Router stability                   : active routers in the mesh backbone
                                         are preferred over REEDs.
    Network depth (hops to Leader)     : considered, but link quality usually
                                         takes precedence.

## Step 3 -- Finalizing the Connection:

  The node sends a unicast Child ID Request to the chosen candidate. The
  parent replies with a Child ID Response that:
    - Confirms the attachment
    - Assigns a Child ID
    - Establishes the parent-child relationship
  The child then derives its RLOC16 from the parent's Router ID.

## Step 4 -- Continuous Re-evaluation:

  Parent selection is not a one-time event:
    Periodic scan   : End Devices periodically scan for better parents.
    Auto-switch     : FTD children may switch to a new parent when a
                      significantly better RSS margin is found.
    Retry backoff   : If a switch attempt fails (e.g., target router is
                      full), the same router cannot be retried for a
                      defined backoff duration.
    SED benefit     : Sleepy End Devices maintain a reliable connection
                      through re-evaluation without needing their radio
                      continuously powered on.

