# Design a leaderboard
## https://leetcode.com/problems/design-a-leaderboard
Design a Leaderboard class, which has 3 functions:

1. addScore(playerId, score): Update the leaderboard by adding score to the given player's score. If there is no player with such id in the leaderboard, add him to the leaderboard with the given score.
2. top(K): Return the score sum of the top K players.
3. reset(playerId): Reset the score of the player with the given id to 0 (in other words erase it from the leaderboard). It is guaranteed that the player was added to the leaderboard before calling this function.

Initially, the leaderboard is empty.

 
```
Example 1:

Input: 
["Leaderboard","addScore","addScore","addScore","addScore","addScore","top","reset","reset","addScore","top"]
[[],[1,73],[2,56],[3,39],[4,51],[5,4],[1],[1],[2],[2,51],[3]]
Output: 
[null,null,null,null,null,null,73,null,null,null,141]

Explanation: 
Leaderboard leaderboard = new Leaderboard ();
leaderboard.addScore(1,73);   // leaderboard = [[1,73]];
leaderboard.addScore(2,56);   // leaderboard = [[1,73],[2,56]];
leaderboard.addScore(3,39);   // leaderboard = [[1,73],[2,56],[3,39]];
leaderboard.addScore(4,51);   // leaderboard = [[1,73],[2,56],[3,39],[4,51]];
leaderboard.addScore(5,4);    // leaderboard = [[1,73],[2,56],[3,39],[4,51],[5,4]];
leaderboard.top(1);           // returns 73;
leaderboard.reset(1);         // leaderboard = [[2,56],[3,39],[4,51],[5,4]];
leaderboard.reset(2);         // leaderboard = [[3,39],[4,51],[5,4]];
leaderboard.addScore(2,51);   // leaderboard = [[2,51],[3,39],[4,51],[5,4]];
leaderboard.top(3);           // returns 141 = 51 + 51 + 39;
```

### Constraints:

1. 1 <= playerId, K <= 10000
2. It's guaranteed that K is less than or equal to the current number of players.
3. 1 <= score <= 100
4. There will be at most 1000 function calls.

## Implementation 1 : Map and TreeMap
```java
class Leaderboard {
    Map<Integer,Integer> map;
    TreeMap<Integer,List<Integer>> tMap;
    public Leaderboard() {
        map = new HashMap<>();
        tMap = new TreeMap<>((a, b) -> b-a);
    }
    
    public void addScore(int playerId, int score) {
        boolean exists = map.containsKey(playerId);
        int prevScore = map.getOrDefault(playerId, 0);
        int newScore = prevScore + score;
        map.put(playerId, newScore);
        // check if the playedId was already there or its new playerId
        if(exists) {
            List<Integer> ids = tMap.get(prevScore);
            int index = ids.indexOf(playerId);
            ids.remove(index);  
        } 
        tMap.putIfAbsent(newScore, new ArrayList<Integer>());
        tMap.get(newScore).add(playerId);
    }
    
    public int top(int K) {
        int selected = 0;
        int sum = 0;
        for(Map.Entry<Integer,List<Integer>> scoreIds : tMap.entrySet()) {
            int score = scoreIds.getKey();
            List<Integer> playerIds = scoreIds.getValue();
            for(int playerId : playerIds) {
                sum += score;
                selected++;
                if(selected == K)
                  return sum;
            }
        }
        return sum;
    }
    
    public void reset(int playerId) {
        // the player is guaranteed to be in tha map before calling reset
        int score = map.get(playerId);
        if(score == 0) 
           return;

        map.put(playerId,0);
        List<Integer> playerIds = tMap.get(score);
        int index = playerIds.indexOf(playerId);
        playerIds.remove(index);
        tMap.putIfAbsent(0, new ArrayList<Integer>());
        tMap.get(0).add(playerId);
    }
}

/**
 * Your Leaderboard object will be instantiated and called as such:
 * Leaderboard obj = new Leaderboard();
 * obj.addScore(playerId,score);
 * int param_2 = obj.top(K);
 * obj.reset(playerId);
 */
```
