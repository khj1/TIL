# Stream

## map

## filter

## forEach

## collect

## count

## reduce

## max

## min

## HashMap 정렬
### Map.Entry
```java
// TODO
public List<Map.Entry<Integer, Integer>> getMost3ChampOfLatestGames(String summonerName) {
    List<MatchReferenceDTO> matches = getLatestSoloRankHistories(summonerName);
    Map<Integer, Integer> champsPlayed = new HashMap<>();

    for (MatchReferenceDTO match : matches) {
        int champion = match.getChampion();

        if (champsPlayed.containsKey(champion)) {
            champsPlayed.put(champion, champsPlayed.get(champion) + 1);
        } else {
            champsPlayed.put(champion, 1);
        }
    }

    return champsPlayed.entrySet()
            .stream()
            .sorted(Map.Entry.<Integer, Integer> comparingByValue().reversed())
            .collect(Collectors.toList())
            .subList(0, 3);
}
```