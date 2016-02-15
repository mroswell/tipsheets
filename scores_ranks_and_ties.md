#Calculating ranks, ordinal number suffixes, and ties from scores

Original column: score

Add columns: rank, rank_text, rank_tie.

Sample desired result:
Rank: 1; Score: 100% (Tie)


Create a rank from team score. Highest score gets lowest rank number.
```sql
UPDATE the_table
SET "rank" = v_table.rnk
FROM
(SELECT team, score, rank()
OVER (ORDER BY score DESC) AS rnk
FROM the_table) AS v_table
WHERE the_table.team = v_table.team
```

Add ordinal number suffixes (for instance, from ranks 1, 2, 3, 4 get 1st, 2nd, 3rd, 4th) 
```sql
UPDATE the_table
SET "rank_text" = v_table.ord
From
(select team, score, rank, to_char(rank, '99th') as ord
from the_table) as v_table
WHERE the_table.team = v_table.team
```

If two or more ranks match (i.e. if the original scores were the same), add the word " (Tie)" This includes the space. 
```sql
UPDATE the_table
SET “rank_tie" = v_table.tie
FROM(SELECT rank, ' (Tie)' AS tie
FROM the_table
GROUP BY rank, tie
HAVING count(*) > 1) as v_table
WHERE the_table.rank = v_table.rank
```

Sample template code
```
<p>Rank: {{rank}}; Score: {{score}}%{{rank_tie}}</p>
```

