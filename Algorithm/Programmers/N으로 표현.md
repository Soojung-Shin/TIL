|    출처    | 프로그래머스 (https://programmers.co.kr/learn/courses/30/lessons/42895) |
| :--------: | ------------------------------------------------------------ |
|  **제목**  | **N으로 표현**                                               |
| **난이도** | Level 3                                                      |

<br />

# N으로 표현

### C++

```c++
#include <string>
#include <vector>
#include <set>
#include <cmath>
using namespace std;

int solution(int N, int number) {
    int count = 1, digit = 0;
    vector<set<int>> dp(9, set<int>());
    
    while(count <= 8) {
        digit += pow(10, count - 1);
        dp[count].insert(digit * N);
        
        set<int>::iterator it1 = dp[count].begin();
        while(it1 != dp[count].end()) {
            if(*it1 == number) return count;
            
            for(int i = 1; i + count <= 8; i++) {
                set<int>::iterator it2 = dp[i].begin();
                
                while(it2 != dp[i].end()) {
                    dp[count + i].insert(*it1 + *it2);
                    dp[count + i].insert(*it1 - *it2);
                    dp[count + i].insert(*it1 * *it2);
                    if(*it2 > 0) dp[count + i].insert(*it1 / *it2);

                    it2++;
                }
            }
            it1++;
        }
        
        count++;
    }
    
    return -1;
}
```

<br />

<br />



### Swift

```swift
import Foundation

func solution(_ N:Int, _ number:Int) -> Int {
    var dp: Array<Set<Int>> = Array(repeating: Set<Int>([]), count: 9)
    var count = 1, digit = 0
    
    while(count <= 8) {
        digit += Int(pow(Double(10), Double(count - 1)))
        dp[count].insert(N * digit)
        
        for num in dp[count] {
            if num == number { return count }
            
            for i in 1 ... 8 {
                if count + i <= 8 {
                    for num2 in dp[i] {
                        dp[i + count].insert(num + num2)
                        dp[i + count].insert(num - num2)
                        dp[i + count].insert(num * num2)
                        if num2 > 0 { dp[i + count].insert(num / num2) }
                    }
                }
            }
        }
        
        count += 1
    }
    
    return -1
}
```

