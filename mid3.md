## 문제 답안

## 내가 쓴 프롬포트 :

- 문제: 정답 숫자 3개(중복 없음)와 사용자가 입력한 숫자 3개가 주어질 때, 볼(Ball)과 스트라이크(Strike) 개수를 계산하여 출력하는 프로그램을 작성하시오.
- 조건:
- 숫자와 위치가 모두 맞으면 Strike, 숫자는 맞지만 위치가 다르면 Ball입니다.
- 3 Strike일 경우 "HOMERUN"을 추가로 출력합니다.
- 입력 예시: 1 2 3 (정답) / 1 3 5 (입력)
- 출력 예시: 1 Strike, 1 Ball


## 코드

#include <stdio.h>

int main() {
    int ans[3];    // 정답 숫자 3개
    int user[3];   // 사용자 입력 숫자 3개
    int strike = 0;
    int ball = 0;

    // 1. 입력 받기 (첫 줄: 정답 3개, 둘째 줄: 사용자 입력 3개)
    // 예시: 1 2 3 / 1 3 5
    scanf_s("%d %d %d", &ans[0], &ans[1], &ans[2]);
    scanf_s("%d %d %d", &user[0], &user[1], &user[2]);

    // 2. 스트라이크 및 볼 판정 로직
    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {
            if (user[i] == ans[j]) {
                if (i == j) {
                    strike++; // 숫자와 위치가 모두 같으면 Strike
                }
                else {
                    ball++;   // 숫자는 같지만 위치가 다르면 Ball
                }
            }
        }
    }

    // 3. 결과 출력
    printf("%d Strike, %d Ball\n", strike, ball);

    // 4. 3 Strike 조건 만족 시 "HOMERUN" 추가 출력
    if (strike == 3) {
        printf("HOMERUN\n");
    }

    return 0;
}
