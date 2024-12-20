# Engineering SDLC v1.0

# Overview

This document describes our Software Development Lifecycle (SDLC). Put simply, it walks you through how we ship software.

# Using This Guide

Treat this guide like a flowchart. Not every step will be relevant to every project, so feel free to skip the sections that aren’t relevant to you.

Below, we’ve also included a “build sheet.” Build sheets are used in auto manufacturing to describe the car a customer wants built. They are usually taped to the car’s body, and follow it along the assembly line. You can use the build sheet here to help you track your projects as they move through our SDLC.

## Build Sheet

### Design

- [ ]  Design doc written
    - PR link:
- [ ]  Design review scheduled
- [ ]  Design review held, and docs ratified
- [ ]  Specs written (as applicable)
    - PR link:
- [ ]  Invariants written
    - PR link:

### Implementation

- [ ]  Code complete
- [ ]  Automated tests written
- [ ]  [If Contracts] Invariant tests written
- [ ]  [If Contracts] Upgrade method implemented

### Validation

- [ ]  FMA written
    - FMA link:
- [ ]  FMA reviewed
- [ ]  FMA action items completed
- [ ]  Audit request approved
- [ ]  Audit commit tagged and frozen
- [ ]  Audit scheduled
    - Audit date:
    - Auditor:
- [ ]  Audit completed
    - Audit report link:
- [ ]  Audit uploaded to security reviews folder
- [ ]  Monitoring/alerting requirements written
    - Link:
- [ ]  Run books written
    - Link:

### Rollout

- [ ]  Release candidates drafted
    - [ ]  op-node
    - [ ]  op-geth
    - [ ]  op-program
    - [ ]  op-contracts
- [ ]  Deployed to internal devnet
- [ ]  Superchain-ops playbook written
    - Playbook link:
- [ ]  Superchain-ops playbook reviewed and merged
- [ ]  Alphanet upgraded
    - HF activation date:
- [ ]  Betanet upgraded
    - HF activation date:
- [ ]  Deployed to Superchain devnet
- [ ]  Testnet scheduled (rollout to all chains we hold keys for)
    - Superchain registry PR:
    - HF activation date:
- [ ]  Deployed to testnet
- [ ]  Mainnet scheduled (rollout to all chains we hold keys for)
    - Superchain registry PR:
    - HF activation date:
- [ ]  Foundation approval requested (2 day SLA)
- [ ]  FND multisig signs (3 day SLA)
- [ ]  Security council signs (3 day SLA, in parallel to Foundation SLA)

### Governance

- [ ]  Governance post drafted
    - Doc link:
- [ ]  Governance post approved by legal and FND
- [ ]  Governance post uploaded
    - Post link:

# SDLC

## Step 0: Ideation and Planning

While beyond the scope of this document, it’s imperative that you know what you’re building and why before development starts. Work with your Product and Engineering Managers if you’re unsure about what you’re building. Typically, there’s a PRD or Problem/Proposed Solution doc that describes the business rationale for our major projects.

1. Start a cross functional discussion with Product, Engineering, DevRel, and PMO to align on the a preliminary scope of work or hypothesis of the work that needs to be delivered. 
2. Typical outputs during Ideation & Planning in which the team builds together:
    1. A PRD or some form of documentation that elaborates on requirements
        1. Acceptance criteria and definition of done 
            1. Spike and/or Prototyping: Help elaborate on the requirements to have a better idea on scope/resource/complexity of the solution
        2. Resourcing & Capacity Planning - Do we have the right people to accomplish the work?
        3. Identify risks and dependencies, and achieve alignment/buy-in
3. Other outputs from this phase that will accelerate the subsequent steps:
    1. Program Dashboard in GitHub
    2. Sequencing and timeline 
    3. Communication Plan (Slack, Discord, Meetings)

## Step 1: Design Review (If Applicable)

Changes that affect multiple teams, deviate from existing practices, introduce new technologies, or change the protocol need to go through Design Review. Design Review helpers ensure alignment between engineering teams and keeps the bar for technical rigor high. It works like this:

1. Create a design doc and post it as a PR in either the [`design-docs`](https://github.com/ethereum-optimism/design-docs) or [`design-docs-private`](https://github.com/ethereum-optimism/design-docs-private) repositories. Use the templates in the repositories.
2. Loop in the leads of each team that might be impacted by your change. These leads are:
    1. **Smart Contracts:** @Kelvin Fichter or @Matt Solomon 
    2. **Infrastructure:** @Zach Howard or @Alfonso Munoz de Laborde 
    3. **Protocol:** @Mark Tyneway or @Proto 
    4. **Proofs:** @Paul Dowman or @Adrian Sutton
3. Announce the PR in the [#pm](https://discord.com/channels/1244729134312198194/1244729134848938033) channel on Discord. Make sure to tag the required domain reviewers to allow for asynchronous review. Leads may tag in additional people as necessary.
4. Schedule a synchronous design review meeting to ratify the design doc and merge the PR. “Ratified” in this case means there’s no more blocking feedback on the design, and merging it into the main branch of the design docs repo. It’s up to the design doc’s author to decide when this is. If your design doc doesn’t get consensus, close the PR instead. **The goal of the discussion is to move towards closure, where closure is either ratifying or rejecting the design doc under review.** Don’t leave a design review hanging without clear actions to move it towards either ratification or rejection.
5. If your change requires a spec (see below), use the design review meeting to decide who the spec reviewers will be.

Keep in mind that the more complex your design is, the longer it will take to review. Use the following SLA as a rule of thumb for how much time to give your reviewers:

- Short document (e.g., 1-pager): share a minimum of 2 working days prior
- Medium-length document (2-5 pages): share a minimum of 3-5 working days prior
- Long document (6+ pages): share at least 1 week prior

For more information about the design review process, see [this doc](https://docs.google.com/document/d/1AvPZMUK5aQjdpw8xAFLkQ7d5sYTgaajkG1IEqgXRcu0/edit?tab=t.0).

## Step 1.5: Write Specs

Changes that modify smart contracts, consensus, or could otherwise be incorporated into alternative client implementations need to be specified in the [`specs`](https://github.com/ethereum-optimism/specs) repo before being rolled out. This is often done in parallel with writing the implementation, since we often find ways to improve the spec while writing the code.

Write your spec by creating a PR against the specs repo and requesting review from one of the specs reviewers identified during the design review. **Loop in the same set of leads as you would for the design doc to review the spec.** They can assign reviewers based on who has the most knowledge of the area of the spec being modified. 

## Step 2: Determine Governance Impact

Once you’ve developed a design for your change, you’ll need to determine if the change requires governance. Changes that affect consensus, touch smart contracts on L1, modify predeploys, or impact transaction ordering will generally require governance approval. If you’re unsure, consult @Ben Jones.

**If YES (Governance Needed):**

- Follow the governance path below. This will include writing a Failure Modes Analysis (FMA), getting audits if needed, and writing a governance proposal.

**If NO (Governance Not Needed):**

- Implement and test, then ship to testnet and mainnet following our rollout procedures. You may still need an FMA - see that section for the criteria.

The detailed criteria for what does/does not require governance is described below.

### Detailed Governance Criteria

<aside>
⚠️ All upgrades which require the Security Council to take action require a governance vote, if they are not an emergency bugfix.
</aside>

The threshold for which changes require a governance vote is based on the User Protections clause of the Law of Chains. In summary, these protections are:

1. **State Transition and Messaging Validity:** OP Chain state transitions or cross-chain messages sent to or from OP Chains must follow the rules of the latest governance-approved release of the OP Stack. This means that changes to the block derivation function or messenger contracts are always subject to a governance vote.
2. **Security, Uptime, and Liveness:** Block production, sequencing, and bridging must satisfy uniform standards for security, uptime, and liveness across all OP Chains. This means that  changes that could cause users to be unable to transact (e.g., changing the gas limit to something untenable) are subject to a governance vote.
3. **Universal, Governance-Approved Upgrades:** OP Chains must upgrade together under OP Stack releases that are approved by governance. Any upgrades that aren’t backwards compatible are therefore subject to a governance vote.

Using this framework, we can define the following rough upgrade types and whether or not each upgrade type needs a governance vote. If you are uncertain if an upgrade requires governance approval, please request delegate feedback on the forum. 

- **Consensus Changes**
    
    **Vote required:** Yes
    
    Consensus changes modify the state transition function or messaging validity. As such, they must be approved by governance to satisfy protection one above. 
    
    For example:
    
    - Bedrock
    - EIP-4844
    - Shanghai
    - Any L1 upgrade that modifies a contract under the control of the Security Council. The Security Council cannot make any changes to L1 unless they are approved by governance *or* the result of an active or impending security issue.
- **Predeploy Updates**
    
    **Vote required:** Yes
    
    Predeploy updates must be approved by governance in order to satisfy protection three above. More specifically, changes to predeploys must be rolled out across all OP Chains in order to prevent functionality on one chain from diverging from all the others.
    
- **Cross-Chain Contracts**
    
    **Vote required:** No
    
    “Cross-chain contracts” refers to smart contracts like Gnosis SAFE or `create2deployer` which are deployed at the same address across multiple chains. These contracts do not require a governance vote because anyone can deploy them at any time on any chain. This is true even if we decide to add these contracts to the genesis state, since someone could always deploy them after the chain comes online.
    
    Note that any changes to the `0x42...` namespace *do* need to go through governance, as do any contract deployments that require irregular state transitions.
    
- **Parameter Updates**
    
    **Vote required:** Change Dependent
    
    Parameter updates that impact protections one or two above will need to be approved by governance. For example, setting the gas limit or changing the EIP-1559 parameters will require governance approval since modifying these parameters can prevent users from transacting.
    
    Examples:
    
    - Updating the ProxyAdmin/challenger/guardian addresses requires a governance vote.
    - Updating gas parameters require a governance vote until they’re explicitly configurable by the Chain Governor
    - Updating the batcher/proposer addresses (among addresses already on the allowlist) do not require a governance vote as long as they are within the set of governance-approved addresses
- **Non-Consensus Client Features**
    
    **Vote required:** No
    
    Network-wide features introduce functionality that may require coordination with alt-client developers, but without risk of a chain split. As such these changes satisfy all three user protections above as long as they are backwards-compatible and meet our bar for engineering rigor.
    
    Examples:
    
    - Snap sync
- **Changes Affecting Transaction Inclusion/Ordering**
    
    **Vote required:** Yes
    
    Even though the mempool is technically not part of consensus, it affects the way in which transactions get included into the chain and can negatively effect user experience. As a result, unilateral changes that affect transaction ordering violate protection two above and therefore need a vote. If the community detects that nonstandard ordering software is being run, it is grounds for removal from the sequencer allowlist.
    
    Examples:
    
    - Moving to a public mempool
    - Running custom PBS/transaction pool software
- **Non-Consensus, No-Coordination, Non-Ordering Changes**
    
    **Vote required:** No
    
    These changes are a catch-all for any change that doesn’t modify consensus or require coordination. These changes can be rolled out unilaterally without input from governance since they do not impact any of the protections described above.
    

*Note: The above sets are not always mutually exclusive. If a given change might fall into multiple buckets, if any one of them requires a vote, then the change requires a vote. If you are unsure if something requires a governance vote, ask @Bobby Dresser or @Ben Jones.*

## Step 3: Implement

At this stage, you can start writing your code. Make sure you follow these standards:

- All consensus code must be behind a hardfork feature flag.
- All changes must go through code review, and have test automation. Use CodeCov to determine how much of your code is tested, and to identify testing gaps.
- All smart contract changes must meet the following minimum standards:
    - Follow the UX and Safety guidelines described [here](https://github.com/ethereum-optimism/design-docs/pull/177).
    - When upgrading existing contracts, follow the spec [here](https://github.com/ethereum-optimism/design-docs/blob/main/protocol/l1-upgrades.md).
    - Have near 100% test coverage along with invariant tests.
    - When useful, changes should be formally verified with Kontrol.

## Step 4: Failure Mode Analysis (If Needed)

In parallel with implementation, you’ll need to get your changes reviewed for security. The primary way we do this is through a Failure Mode Analysis (FMA). The FMA process identifies risks and failure modes so that we can mitigate them prior to release. FMAs work best when they are written early in the development process, and iterated upon as work progresses.

An FMA is always required if governance is needed. Otherwise, it’s up to each team and their embedded security engineers if they need one. The process to get an FMA is described here: [Failure Mode Analyses (FMAs)](https://www.notion.so/Failure-Mode-Analyses-FMAs-1fb9f65a13e542e5b48af6c850763494?pvs=21), and FMAs live in the design-docs repo using this template: https://github.com/ethereum-optimism/design-docs/blob/main/assets/fma-template.md.

## Step 5: Security Audit (If Needed)

The FMA may identify the need for a security audit. If so, copy the document [here](https://docs.google.com/document/d/1iJj5kNPvw8Lyov_MYAhFSeDVQtIg14w0L1hjWDHaVSI/edit?tab=t.0) and use it as a template to describe the scope, timing, and motivation for the audit. Send this to @Prithvi Subburaj and @Karl Floersch for approval. Once they’ve approved, you can shop around for auditors and get quotes. Once you’ve selected an auditor you can create request in Zip to pay for the audit.

Some audit firms we’ve used successfully in the past include:

- Spearbit
- OpenZeppelin

You should factor the amount of time required for both the audit as well as and necessary fix review into delivery timelines.

**Your code should be deployed on a devnet prior to auditing.** This can be one of our monthly devnets, or a custom internal devnet for the purposes of performing the audit.

## Step 6: Create Superchain Ops Tasks (L1 Upgrades Only)

<aside>
⚠️

This section is under construction. Contact @Matt Solomon for questions regarding Superchain Ops tasks.

</aside>

If your change modifies L1 smart contracts, you’ll need a `superchain-ops` playbook to execute the multisig transactions with the Security Council.

## Step 7: Alphanet/Betanet Devnet Rollout

Next, it’s time to roll out to the Alphanet, then the Betanet. The full process for this is documented here: [Devnet Operations](https://www.notion.so/Devnet-Operations-15af153ee1628024913ae6c458715131?pvs=21). Make sure to take the timeline into account. Devnets are created on the first Wednesday of every month, so your devnet artifacts need to be ready at least 5 days beforehand.

## Step 8: Testnet Rollout

Next, it’s time to roll out to the official testnet. These networks upgrade multiple chains at once, so they require coordination with DevRel and external partners. These networks are also considered production, so a high degree of stability is expected.

The process to upgrade these networks is:

1. (As Applicable) Update the Superchain Registry with an updated hardfork time.
2. (As Applicable) Update OPCM/op-deployer to upgrade smart contracts.
3. Cut an `rc` build like you would normally.
4. Loop in @Matthew Cruz to schedule your upgrade. Typically partners need at least a week of notice.
5. Use `op-workbench` to deploy onto our infrastructure.
6. Use `op-deployer` to upgrade L1 smart contracts.

## Step 9: Governance Proposal (If Governance Is Needed)

1. **Prepare Proposal:**
    - Reference a stable commit/tag.
    - Include FMA results, audits, testnet performance, and activation schedule.
    - Use the standard governance [template](https://gov.optimism.io/t/season-6-charter-aware-upgrade-proposal-template/8134).
2. **Review & Post:**
    - Obtain Foundation (FND) and Legal approval.
        - Loop in @Ben Jones and @Bobby Dresser from the FND.
        - Loop in @Trevor Dodge and @Eric Van Wart from legal.
    - Post the proposal on governance forums.
    - Loop in @Lavande to coordinate delegate approval.
3. **Approval & Veto:**
    - Wait for the vote and veto period to complete.

## Step 10: Mainnet Rollout

1. Remove the `rc` suffixes from your releases.
2. Schedule the mainnet upgrade after the veto period expires.
3. Coordinate with @Maurelian or @Blaine Malone to schedule the mainnet multisig upgrade.
4. The Product/Dev/PMO Leads to go through their checklist to insure all stakeholder needs, documentation, and communications are in place. This includes working with Marketing, DevRel, Data, Finance, Foundation, etc.