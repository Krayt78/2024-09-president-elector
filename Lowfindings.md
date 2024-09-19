## L-01 Voters can vote multiple times for the same Candidate
## Summary

Whenever going over the s_candidateVotesByRound for a Voter, the contract never checks if one candidate has already been used.

## Vulnerability Details
A regular vote should look like this [0,1,2,3,4,5,...,9](should be addresses instead) where someone ranks the 10 candidates is some random order .
The probleme here is that when calculating the points in _selectPresidentRecursive we dont check if a candidate has already been voted for
aka nothing is stoping us from doing something like this [3,3,3,3,3,3,...,3] which would bypass the whole point of ranking. 
This would result in the end to vote only for one person.
This has not been marked as being a known issue.

## Impact
Higher gas fees

## Tools Used 
N/A

## Recommended Mitigation

I suggest checking the orderedCandidates array given to us in _rankCandidates.
Something along those lines : 

```  js
    function _rankCandidates(
        address[] memory orderedCandidates,
        address voter
    ) internal {
        // Checks
        if (orderedCandidates.length > MAX_CANDIDATES) {
            revert RankedChoice__InvalidInput();
        }
        if (!_isInArray(VOTERS, voter)) {
            revert RankedChoice__InvalidVoter();
        }

        address[] memory tempCandidateList = new address[](MAX_CANDIDATES);

        for (uint i = 0; i < orderedCandidates.length; i++) {
            if(orderedCandidates[i] == address(0)) {
                revert RankedChoice__InvalidInput();
            }

            if(_isInArray(tempCandidateList, orderedCandidates[i])) {
                revert RankedChoice__InvalidInput();
            }

            tempCandidateList[i] = orderedCandidates[i];
        }

        // Internal Effects
        s_rankings[voter][s_voteNumber] = orderedCandidates;
    }
```


## H-01 Voters can vote multiple times for the same Candidate
## Summary

(Provide a brief overview of the vulnerability.)

## Vulnerability Details

(Delve deep and elaborate on the identified issue, including where it exists in the codebase.)

## Impact

(Describe the potential consequences of this vulnerability. How could it harm the protocol or users?)

## Tools Used 

(List any tools or software that aided in the identification of the vulnerability.)

## Recommended Mitigation

(Suggest ways to resolve or mitigate the identified vulnerability.)