# Bitcoin Lightning Network Lab Report
## Node Expansion, Multi-Hop Payments, and Routing Fee Configuration Using Polar and LND

### 1. Introduction

This practical exercise explored the operation of the Bitcoin Lightning Network using Polar, a local development environment for simulating Lightning nodes and payment channels. The primary objective was to understand how nodes communicate, how payment channels facilitate transactions, and how routing fees can be configured to influence payment forwarding.

The lab began with a two-node network and was expanded by adding a third node. The nodes were named **Akinola, Olamilekan, and Luqmon**. The exercise also involved making payments between nodes and modifying the forwarding fee policy of Olamilekan to examine the relationship between routing costs and multi-hop payments.

All activities were conducted in a controlled local testing environment using Bitcoin regtest and LND.

### 2. Objectives

The objectives of the exercise were to:

1. Create and configure a Lightning Network test environment using Polar.
2. Expand the network from two nodes to three nodes.
3. Establish payment channels between the nodes.
4. Execute Lightning payments between connected nodes.
5. Configure Olamilekan's forwarding fee rate from 1,000 parts per million (ppm) to 2,000 ppm.
6. Understand how routing policies affect multi-hop payments.
7. Develop practical troubleshooting and node-management skills.

### 3. Tools and Environment

The following tools and components were used:

- **Polar:** For creating and managing the local Lightning Network.
- **Bitcoin regtest:** For conducting controlled blockchain transactions without using real Bitcoin.
- **LND v0.20.0-beta:** For managing Lightning nodes, invoices, payment channels, and routing policies.
- **Linux terminal:** For executing commands and inspecting node information.

The three nodes were named Akinola, Olamilekan, and Luqmon.

### 4. Network Configuration and Expansion

The initial network consisted of two Lightning nodes. The network was subsequently expanded by adding a third node through the Polar interface.

The resulting topology was designed to support a multi-hop payment path:

**Akinola → Olamilekan → Luqmon**

In this arrangement, Akinola acts as the initiating or sending node, Olamilekan serves as the intermediate forwarding node, and Luqmon acts as the receiving node.

Payment channels provide the mechanism through which nodes can transfer funds off-chain. Rather than requiring every payment to be recorded directly on the Bitcoin blockchain, Lightning payments update channel balances, subject to the network's routing and liquidity constraints.

### 5. Invoice Creation and Payment Testing

An invoice was generated on the receiving node, Luqmon, to request a payment of 10,000 satoshis. The invoice was configured to include routing hints for private channels, helping the sending node discover a viable route through the intermediate node.

The payment was initiated from Akinola using the LND command-line interface. The invoice was supplied to the payment command, and the transaction was confirmed when prompted.

The payment process was intended to test whether Akinola could reach Luqmon through Olamilekan, even though the nodes did not need to share a direct payment channel.

The successful multi-hop payment demonstrated in the earlier exercise confirmed that Lightning payments can travel through an intermediate node. For the latest test following the fee change, the final payment result should be recorded separately to establish whether the new policy was applied successfully.

### 6. Modification of the Routing Fee Policy

A central part of the exercise was modifying the forwarding fee policy on Olamilekan's channel to Luqmon.

The initial fee rate was 1,000 ppm. It was changed to 2,000 ppm using the LND `updatechanpolicy` command.

The configured policy was:

| Parameter | Configured value | Meaning |
|---|---:|---|
| Base fee | 1 sat | Fixed component of the forwarding fee |
| Fee rate | 2,000 ppm | Proportional component of the fee |
| Time-lock delta | 80 blocks | Configured forwarding timelock delta |

The fee rate was expressed as:

2,000 ppm = 0.002 = 0.2%
The channel point identifies the specific channel whose policy was being updated. Using the correct channel point is essential because a node may manage multiple channels, each with its own forwarding policy.

The fee configuration was subsequently checked with:

```bash
lncli feereport
```

This command displays the node's forwarding fee policies and can be used to verify whether the configured rate has changed to 2,000 ppm.

### 7. Understanding the Routing Fee

The forwarding fee generally consists of a base fee and a proportional fee calculated from the forwarded amount.

A simplified calculation is:

**Forwarding fee = Base fee + (Forwarded amount × Fee rate)**

For a 10,000-sat payment and a fee rate of 2,000 ppm:

- Proportional component: 10,000 × 0.002 = 20 sats.
- Base component: 1 sat.
- Illustrative forwarding fee: 21 sats.

This calculation illustrates the intended policy. The actual fee charged to the sender can also depend on the policies of other hops and the route selected by the payer. Therefore, the payment output and routing policy reports should be examined together before attributing the entire fee to one node.

Increasing the rate from 1,000 ppm to 2,000 ppm doubles the proportional component of the fee. It does not necessarily double the total payment fee because the base fee and any fees charged by other nodes remain relevant.

### 8. Technical Lessons and Observations

The exercise provided practical experience in several aspects of Lightning Network operation.

**First, network topology matters.** A multi-hop payment depends on a suitable path connecting the sender and receiver. Nodes do not require a direct channel with every possible recipient, but intermediate nodes must have usable channels and sufficient liquidity.

**Second, private channels require additional routing information.** A private channel is not generally advertised in the same way as a public channel. Routing hints in an invoice can help a payer discover a path to a recipient through such a channel.

**Third, forwarding fees are configurable.** Node operators can set base fees and proportional fee rates. These policies affect the cost of forwarding payments and may influence which routes are economical for senders.

**Fourth, a successful policy update does not guarantee a successful payment.** A payment can still fail because of insufficient liquidity, unavailable routes, channel restrictions, or other routing conditions.

**Finally, verification is an essential operational practice.** Commands such as `feereport`, `getinfo`, `lookupinvoice`, and payment-status inspection provide evidence that the network and its policies are behaving as expected.

### 9. Conclusion

This lab exercise developed practical understanding of Lightning Network node management through Polar and LND. The network was expanded from two nodes to three, with Akinola as the sending node, Olamilekan as the intermediate node, and Luqmon as the intended recipient.

The exercise also covered invoice generation, multi-hop payment testing, private-channel routing hints, and the modification of a forwarding fee policy from 1,000 ppm to 2,000 ppm.

These activities demonstrate the importance of understanding network topology, channel liquidity, routing policies, and payment verification when operating a Lightning node. The lab provides a foundation for further experiments involving liquidity management, fee optimization, channel rebalancing, node monitoring, and operational security.

### 10. Evidence to Attach

For a complete technical portfolio entry, the following evidence should be collected and attached:

1. A screenshot of the three-node Polar network.
2. A screenshot showing the payment channels and their active status.
3. The output of `lncli feereport` showing the updated 2,000 ppm policy.
4. The payment output showing the final status, amount received, fee, and route.
5. The receiver's invoice lookup confirming settlement.

These records will make the report reproducible and distinguish verified results from the objectives of the latest payment test.
