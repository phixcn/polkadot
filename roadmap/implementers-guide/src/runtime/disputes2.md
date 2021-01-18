# Disputes

## Motivation

If the transaction can be verified the defined verification code, it is good. If not, there is a dispute
about a block, which made it through the backing stage, but was then during secondary checking found
to be invalid.
At this point the validator set can not be trusted (since they voted for the block despite something being
fishy at the very least) or the validator that blew the whistle has ulterior motives to do so.
In either way, there are malicious validators around.
As a consequence, all validators at the time of block backing, are being notified via broadcast of the first challenging vote.
Validators that backed the candidate implicitly voted for backing the candidate and may not vote a second time.
That would be equal to an attempted double vote and would be slashed accordingly.

## Initiation

A dispute is initiated by one secondary checker creating and gossiping a vote, that challenges the vote.
Secondary checkers run the validation code and use this to verify the transactions.

After a secondary checker challenged a block, all validators that received the gossiped vote, check the
block themselves via the validation code.
The result of that check is converted into a vote, and distributed via the same mechanics as the first one.

Once a receiver receives quorum and supermajority in one or the other direction, the
vote is concluded.
Conclusion implies that the result for this block can not be altered anymore, valid or invalid is fixed now.

In order to assure, the dispute result is not forgotten. It has to be recorded on chain.
This on chain recording mechanic must be vigilant, in a sense, that new emerging forks
must also receive the dispute resolution recorded (transplantation) iff the disputed block is container there or is in the
ancestry path.

If the disputed block was already finalized, the block must be put in governance mode to be resolved be by hand
(i.e. sudo or motion or other mechanics that are available ).

Now there might be the case that the network is fragmented for a limited amount of time. If the dispute would happen
during this time, a block could be disputed, that we have never seen being part of any chain, but we might get
a vote for it. That is called a "remote" dispute.

As such the validator has to keep track of all votes irrespective if the disputed block is already known or not.
All backing votes should be either kept in storage as well, or be queried on demand, since they are a kind of vote
as well.

## Late votes

Late votes, after the dispute already reached a quorum, must be rewarded (albeit a smaller amount) as well.
These ones must be attached to the votes after a defined period of time after the result has reached
the required quorum + supermajority.

## Chain selection

Chain selection should be influenced by the chance of picking a chain that does not even include the disputed block.
Hence removing the need to include the dispute resolution itself.
This is only possible though, if the set of active heads contains such a fork.
