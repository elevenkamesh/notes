# 
https://leetcode.com/problems/two-sum/

```java
class Solution {

    public int[] twoSum(int[] nums, int target) {

        Map <Integer , Integer > comp = new HashMap<>();

        for(int i = 0 ; i < nums.length ; i++){

                int dif = target - nums[i]  ;

                if(comp.containsKey(dif)){

                    return new int[]{i , comp.get(dif)};

                }

                comp.put(nums[i] , i );

        }

        return new int[] {};

    }

}


```


```java
class Solution {

    public int[] twoSum(int[] nums, int target) {

        HashMap<Integer , Integer> hm = new HashMap<>();

        int len = nums.length ;

        for( int i  = 0  ; i < len ; i++ ){

            int dif =  target  - nums[i] ;

            if( hm.containsKey(dif)){

                return new int[]{ hm.get(dif) , i };

            }

            hm.put(nums[i] , i );

        }

        return new int[]{-1 , -1 };

    }

}
```
