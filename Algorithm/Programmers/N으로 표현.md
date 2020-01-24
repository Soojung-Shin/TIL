|    출처    | 프로그래머스 (https://programmers.co.kr/learn/courses/30/lessons/42895) |
| :--------: | ------------------------------------------------------------ |
|  **제목**  | **N으로 표현**                                               |
| **난이도** | Level 3                                                      |

<br /><br />

# N으로 표현

### 풀이

동적계획법을 이용해 풀었다. 우선 `dp`라는 Set으로 이루어진 Array를 만든다. 카운트를 인덱스로 해서 해당 카운트에 나올 수 있는 값들을 저장했다. Set을 이용했기 때문에 중복값이 들어가지 않아서 중복된 계산을 피할 수 있다.

값은 연산자 없이 붙여서 표현할 수 있기 때문에 `dp`에 초기값으로 `1`, `11`, ..., `11111111` 를 넣어준다. 카운트는 `8`을 넘을 수 없기 때문에 여덟자리까지 계산해 넣어주었다.

그리고 `dp` 를 순회하면서 현재 카운트와 더해서 8보다 작은 배열의 값들을 `+`, `-`, `*`, `/` 연산으로 계산해 넣어준다. 이때 `/` 연산에서 나누는 값이 `0`이 되면 안되기 때문에 if문으로 처리해주었다.

카운트가 `8`이 될 때까지 위 동작을 반복한다. 타겟 `number` 와 같은 값을 발견하면 해당 카운트를 반환한다. 현재 카운트가 `8`을 넘어가면 조건 안에 계산할 수 없는 수이므로` -1`을 반환한다.

<br /><br />

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

<br />

<br />

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

