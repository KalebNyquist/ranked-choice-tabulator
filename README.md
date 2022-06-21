# Nyquist's Ranked Choice Voting Tabulator

This is my sandbox for exploring the results of [ranked choice voting](https://en.wikipedia.org/wiki/Ranked_voting) elections. There are many other fine tabulators out there, however none met my particular needs. This tabulator has the following distinctives that you may also find useful:

* **Notebook-based** — Many public policy data analysts like myself prefer notebooks that strike a balance between agility and being shareable. In particular, I have designed this tabulator to be accessible online  (i.e., open tabulator.ipynb in a Google Collab notebook), requiring that no package needs to downloaded onto your own computer in order to get started. 
* **Parametric importing** — Instead of taking an approach that hardcodes a translation between a select file formats, this tabulator allows you to specify parameters that guide the import process of ranked choice election data. This additional flexibility makes it possible to gathering election data from a diverse range of sources, including those who use idiosyncratic formatting.
* **Non-monotonicity detection** — The tabulator is able to detect when ranked choice elections are non-monotonic (i.e., there exists a group of voters for whom ranking a given candidate higher will cause that candidate to lose the election). In simple cases of non-monotonicity, it is possible to retrieve a "lower range" and "higher range" of non-monotonicity representing the minimum and maximum number of voters who can cause this undesirable result.
* **Narrative reporting** — In addition to a horizontal bar graph that visualizes the consecutive rounds of a ranked choice election, it is possible to get the results reported in plain English. This verbal accompaniment can be a useful supplement to a visual report of election results, or can also be used as a standalone to make the results accessible in an audio format (i.e., as a radio and podcast script, or for the visually-impaired).



## Non-monotonicity detection

In social choice theory, [Arrow's Impossibility Theorem](https://en.wikipedia.org/wiki/Arrow's_impossibility_theorem) states a set of desirable conditions that are impossible for any voting method to meet all at once. Ranked choice voting fails to meet the monotonicity criterion, meaning that it is theoretically possible for a voter to cause a candidate to lose by ranking that candidate higher than she otherwise would have. 

Because the idea that ranking a candidate higher could cause a candidate to lose is an absurd outcome and an undesirable characteristic of ranked choice voting, advocates of ranked choice voting tend to overlook non-monotonicity in their analyses. However, critics of ranked choice voting have a similar tendency to omit an upper bound to the number of voters who can cause a non-monotonic result. This tabulator takes a "both-and" approach, attempting to detect non-monotonic results but also providing an upper bound to illustrate that the possibilities for non-monotonicity are usually few and always finite.

The guiding assumption of the algorithm for detecting non-monotonicity is that the set of candidates who compete in the final round of voting is a function of the order in which other candidates were eliminated. Therefore, various elimination orders (permutations of the non-winning candidates) are forced upon the results to see if a different candidate can emerge victorious over the real winner in the final round of voting. If so, additional math is used to determine the third candidate who had to lose votes to the real winner for this result to occur, and then determine the lower and upper range of non-monotonicity.

I am continuing to test this algorithm to verify that it has been set up correctly. There may still be false negatives but there should be not any false positives. Notably, it does produce the correct result for the 2009 Burlington Mayoral election, perhaps the canonical instance of non-monotonicity for ranked choice elections in the United States.



## Development Roadmap

I plan to continue developing this tabulator as I explore electoral system reform as a sustained research interest. These are some features in particular that I have in mind.

* **Rules Configurations** — At present, only a specific rule set is supported (i.e., instant runoff where lowest-ranked candidates are eliminated until a candidate gets 50% + 1 of the votes). Tiebreaker logic is hard-coded into the data at various points rather than allowing the user to determine how ties are resolved (both at the level of ties on an individual ballot, and ties for victory/elimination in a critical round of voting). Furthermore, the minimum threshold for a candidate to qualify is set at just 1 highest-ranking in the first round of voting.
* **Support for multi-winner elections** — Right now, the series of "instant-runoffs" ends when one candidate has secured election, reducing the opportunities for analysis in the case of proportional representation. If and when implemented, support for multi-winner elections will support data from approval voting elections' cast vote records as well.
* **Retrieve ranges of non-monotonicity in complex cases, other improvements** — Right now the non-monotonicity detection algorithm halts analysis in instances where there exist _multiple_ candidates for whom if they are ranked lower will paradoxically win the election. Similarly, the algorithm needs some subtle tweaking for elections where the winner is actually non-monotonic (versus elections where there is a non-monotonic range where the winner may be dethroned by a non-monotonic rival).
* **Support for interpreting ballot weights when importing (used for compressing a cast vote record file size by grouping together identically-ranked ballots from different voters)** 
* **Support for when candidates are listed in the columns and their ranks are the field values (the inverse of the `field` Record Structure** 
* **Customizable Horizontal Bar Graph**
* **Parameterized JSON importing** and **Universal RCV Tabulator JSON format exporting**
* As always, minor tweaks and [more human-readable code](https://twitter.com/martinfowler/status/1067179181140844544) remain an ongoing process.



## Personal Note

Beyond the data-driven analysis that I have brought together in this repository, I have written elsewhere about [the "spiritual" criteria for evaluating electoral reforms](https://thefulcrum.us/Elections/Voting/faith-and-voting) such as ranked choice and approval voting. 

