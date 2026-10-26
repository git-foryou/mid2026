## 문제 답안

## 내가 쓴 프롬프트 :
아스키 피라미드 패턴 출력
- 문제: 높이 N을 입력받아 중앙 정렬된 피라미드 형태의 문자 패턴을 출력하시오.
- 조건: 홀수 번째 행은 *, 짝수 번째 행은 #으로 출력합니다.
- 입출력 예시 (N=4):
  
      *
     ###
    *****
   #######


## 코드 : 

    #include <stdio.h>
    
    int main() {
        int N;
        
        // 높이 N 입력 받기
        if (scanf("%d", &N) != 1) return 0;
    
        for (int i = 1; i <= N; i++) {
            // 1. 행 번호에 따라 출력할 문자 결정 (홀수 행: '*', 짝수 행: '#')
            char symbol = (i % 2 != 0) ? '*' : '#';
    
            // 2. 좌측 여백(공백) 출력: N - i 개
            for (int j = 0; j < N - i; j++) {
                printf(" ");
            }
    
            // 3. 패턴 문자 출력: 2 * i - 1 개
            for (int j = 0; j < 2 * i - 1; j++) {
                printf("%c", symbol);
            }
    
            // 4. 줄 바꿈
            printf("\n");
        }
    
        return 0;
    }

<img width="1099" height="579" alt="image" src="https://github.com/user-attachments/assets/ea32e2a3-bbd2-48ea-9d38-956422096cda" />


