# Disputes

## Motivation

If the transaction can be verified the defined verification code, it is good. If not, there is a dispute
about a block, which was either detected during the backing stage by a backing checker
or, made it through the backing stage, but was then found invalid by an approval checker.

(If there was no conflict between votes, but nobody backed the block in the first place, it is TBD if that falls in the category of a dispute or not)

At this point the validator set can not be trusted (since they voted for the block despite something being
fishy at the very least) or the validator that blew the whistle has ulterior motives to do so (i.e. it is controlled by a third party and wants to incur damage to itself).
In either way, there are malicious validators around.
As a consequence, all validators at the time of block backing, are being notified via broadcast of
the first challenging vote.
Validators that backed the candidate implicitly voted for backing the candidate and may not vote a second time in different fashion, i.e. a pro and a con vote would be punished, two positive votes would be fine, but the first would be counted.
Two opposing votes by the same validator would be equal to an attempted double vote and would be slashed accordingly.

All validators at block inclusion time are eligible to (and should) cast their Vote. The backing votes of backing checkers
are counted as votes as well.

## Initiation

A dispute is initiated by one approval checker creating and gossiping a vote, that challenges the vote.
approval checkers run the validation code and use this to verify the transactions.

After a approval checker challenged a block, all validators that received the gossiped vote, reconstruct the block
from availability erasure code chunks and check the block themselves via the validation code.
The result of that check is converted into a vote, and distributed via the same mechanics as the first one.

Once a receiver receives quorum and supermajority in one or the other direction, the
vote is concluded.
Conclusion implies that the result for this block can not be altered anymore, valid or invalid is fixed now.

In order to assure, the dispute result is not forgotten or intentionally side stepped, it has to be recorded on chain.
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
