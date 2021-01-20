# Disputes

## Motivation

A reconstructed PoV can be verified with the defined verification code, that is valid during the session the block was included.
If the block is invalid and there exists at least one backing vote and one disputing vote, a dispute exists,
which was either detected during the backing stage by a backing checker
or, made it through the backing stage, but was then found invalid by an approval checker which
propagate his vote.

At this point the set of backing validators can not be trusted (since they voted for the block despite something being
fishy at the very least). On the other hand, one must also consider, the validator that blew the whistle has ulterior motives
to do so (i.e. it is controlled by a third party and wants to incur damage to itself).
In either way, there are malicious validators around.
As a consequence, all validators at the time of block backing, are being notified via broadcast of
the first challenging vote.
Validators that backed the candidate implicitly count as votes. Those validators are allowed to cast
a regular vote (a non-backing vote) as well, but it is generally not in their interest to vote both sides, since that would
advance the progress towards super majority either way and have their bonds slashed.
If both votes lean in the same direction, i.e. both positive they are only counted as one.
Two opposing votes by the same validator would be equal to an attempted double vote and would be slashed accordingly.

All validators at block inclusion time are eligible to (and should) cast their Vote. The backing votes of backing checkers
are counted as votes as well.

## Initiation

A dispute is initiated by one approval checker creating and gossiping a vote, that challenges the vote.
approval checkers run the validation code and use this to verify the transactions.

After an approval checker challenged a block, all validators that received the gossiped vote, reconstruct the block
from availability erasure code chunks and check the block themselves via the validation code.
The result of that check is converted into a vote, and distributed via the same mechanics as the first one.

Once a receiver receives quorum and supermajority in one or the other direction, the
vote is concluded.
Conclusion implies that the result for this block can not be altered anymore, valid or invalid is fixed now.

In order to ensure, the dispute result is not forgotten or intentionally side stepped, it has to be recorded on chain.
This on chain recording mechanic must be vigilant, in a sense, that new emerging forks
must also receive the dispute resolution recorded (transplantation) iff the disputed block is in the
ancestry path of that chain.

If the disputed block was already finalized, the block must be put in governance mode to be resolved be by hand
(i.e. sudo or motion or other mechanics that are available ).

As such the validator has to keep track of all votes irrespective if the disputed block is already known or not.
All backing votes should be either kept in storage as well, or be queried on demand, since they are a kind of vote
as well.

## Late votes

Late votes, after the dispute already reached a quorum + super majority, must be rewarded (albeit a smaller amount) as well.
These ones must be attached to the votes after a defined period of time after the result has reached
the required quorum + supermajority.

## Chain Selection / Grandpa

Chain selection should be influenced by the chance of picking a chain that does not even include the disputed block.
Hence removing the need to include the dispute resolution itself.
This is only possible though, if the set of active heads contains such a fork.
In Grandpa the Voting rule should be used to avoid finalizing chains that contain an open or negative shut (shut with super majority that marks the block as invalid) dispute.
In case all possible chains contains such a dispute, a TBD metric must be used to decide which fork to use or avoid finalization until one dispute resolves positive (the
block is valid).
