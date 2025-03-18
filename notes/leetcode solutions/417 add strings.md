
```java
import java.util.*;

class Solution {
    public String addStrings(String num1, String num2) {
        // Initialize variables
        StringBuilder result = new StringBuilder();
        int carry = 0;
        int i = num1.length() - 1;
        int j = num2.length() - 1;

        // Loop through both strings from the end to the beginning
        while (i >= 0 || j >= 0 || carry > 0) {
            int a = (i >= 0) ? num1.charAt(i) - '0' : 0; // Get digit from num1 or 0 if out of bounds
            int b = (j >= 0) ? num2.charAt(j) - '0' : 0; // Get digit from num2 or 0 if out of bounds
            int sum = a + b + carry; // Calculate the sum of digits and carry
            carry = sum / 10; // Update carry for next iteration
            result.append(sum % 10); // Append the last digit of the sum to the result
            i--;
            j--;
        }

        // Reverse the result to get the final sum
        return result.reverse().toString();
    }
}

```