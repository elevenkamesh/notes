
```java
import java.util.*;

  

class Solution {

    public List<List<String>> groupAnagrams(String[] strs) {

        // HashMap to store sorted string as key and list of anagrams as value

        HashMap<String, List<String>> hm = new HashMap<>();

  

        for (String s : strs) {

            // Convert the string to a character array and sort it

            char[] word = s.toCharArray();

            Arrays.sort(word);

  

            // Convert the sorted character array back to a string

            String sortedString = new String(word);

  

            // If the sorted string is already a key, add the original string to the list

            if (hm.containsKey(sortedString)) {

                hm.get(sortedString).add(s);

            } else {

                // If not, create a new list with the original string and put it in the map

                hm.put(sortedString, new ArrayList<>(Arrays.asList(s)));

            }

        }

  

        // Return all grouped anagrams as a list of lists

        return new ArrayList<>(hm.values());

    }

}
```


```java
class Solution {

    public List<List<String>> groupAnagrams(String[] strs) {

        HashMap < String , List<String>> map = new HashMap <>();

    for (String s : strs){

  

        String  sortedString = getfreq(s);

  

        if(map.containsKey(sortedString)){

            map.get(sortedString).add(s);

        }else{

            List<String> l = new ArrayList<>();

            l.add(s);

            map.put(sortedString , l);

  

        }

  

    }

  

return new ArrayList(map.values());

    }

  
  

    public String getfreq(String s ){

        char[] arr = s.toCharArray();

        Arrays.sort(arr);

        return new String(arr);

    }

}
```

pedro tech 
namaste js
react namaste js
jscafe
roadside coder

