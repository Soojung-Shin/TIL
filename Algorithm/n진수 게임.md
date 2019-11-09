|    출처    | 프로그래머스 (https://programmers.co.kr/learn/courses/30/lessons/17687) |
| :--------: | ------------------------------------------------------------ |
|  **제목**  | **n진수 게임**                                               |
| **난이도** | Level 2                                                      |

<br />

# n진수 게임

### 풀이 과정

어떤 수의 n진수를 구하는 법은 해당 숫자를 n으로 나누고 나머지를 취한다. 이를 숫자가 0이 될 때까지 반복하고, 마지막에 그 숫자를 뒤집는다. 나머지가 두 자릿수 일 때는 A부터 알파벳을 이용해 표시한다. 이렇게 구한 n진수들을 string 형으로 쭉 연결해 저장한다.

튜브가 뽑아야 하는 `t`개의 숫자 혹은 알파벳을 구할 수 있을 충분한 수까지 n진수를 구하고, 튜브의 순서에 말해야 하는 숫자 혹은 알파벳을 `t`개 선택해 반환한다.

`t`개의 숫자 혹은 알파벳을 구할 수 있을 충분한 수를 `t * m`으로 지정했다.

<br />

### 구현

```c++
#include <string>
#include <vector>
#include <algorithm>
using namespace std;

string getString(int n, int number) {
    string s = "";
    while(number > 0) {
        if(number % n >= 10) {
            char ch(number % n + '7');
            s += ch;
        } else {
            s += to_string(number % n);
        }
        number /= n;
    }
    reverse(s.begin(), s.end());
    return s;
}

string solution(int n, int t, int m, int p) {
    string answer = "";
    
    string s = "0";
    for(int i = 1; i < t * m; i++) {
        s += getString(n, i);
    }
    
    int idx = p - 1;
    for(int i = 0; i < t; i++) {
        answer += s[idx];
        idx += m;
    }
    
    return answer;
}
```

`char ch(number % n + '7')` 에서 `'7'` 은 아스키코드 `55`를 의미한다. 알파벳 `A` 는 아스키코드 `65`이기 때문에 나머지가 `10` 보다 같거나 큰 경우 `55`를 더해주면 해당하는 알파벳을 구할 수 있다.
