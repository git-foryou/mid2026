## 문제 답안

## 쓴 프롬포트 : 
  - SQLite를 활용한 간단한 DB를 설계하고 구현 하시오. 시나리오는 각자 만드는데,
  -  반드시 3명 이상의 데이터를 넣고,
  -  검색을 포함해야 합니다.

## 코드 :

    #include <stdio.h>
    #include <stdlib.h>
    #include <sqlite3.h>
    
    // 데이터 조회를 처리할 콜백 함수
    static int callback(void* NotUsed, int argc, char** argv, char** azColName) {
        for (int i = 0; i < argc; i++) {
            printf("%s: %s  ", azColName[i], argv[i] ? argv[i] : "NULL");
        }
        printf("\n");
        return 0;
    }
    
    int main() {
        sqlite3* db;
        char* err_msg = 0;
        int rc;
    
        // 1. 데이터베이스 연결 (파일 생성: company.db)
        rc = sqlite3_open("company.db", &db);
        if (rc != SQLITE_OK) {
            fprintf(stderr, "데이터베이스 열기 실패: %s\n", sqlite3_errmsg(db));
            sqlite3_close(db);
            return 1;
        }
    
        // 2. 테이블 생성 SQL
        const char* sql_create = "CREATE TABLE IF NOT EXISTS employees (" \
            "id INTEGER PRIMARY KEY AUTOINCREMENT, " \
            "name TEXT NOT NULL, " \
            "department TEXT NOT NULL, " \
            "skill TEXT NOT NULL, " \
            "experience_years INTEGER);";
    
        rc = sqlite3_exec(db, sql_create, 0, 0, &err_msg);
        if (rc != SQLITE_OK) {
            fprintf(stderr, "테이블 생성 실패: %s\n", err_msg);        sqlite3_free(err_msg);
            sqlite3_close(db);
            return 1;
        }
    
        // 3. 3명 이상의 데이터 삽입 SQL (총 4명)
        const char* sql_insert = "INSERT INTO employees (name, department, skill, experience_years) VALUES " \
            "('김철수', '개발팀', 'Python', 3), " \
            "('이영희', '기획팀', 'UI/UX 디자인', 5), " \
            "('박민수', '개발팀', 'Java', 4), " \
            "('정지원', '마케팅팀', 'Google Analytics', 2);";
    
        rc = sqlite3_exec(db, sql_insert, 0, 0, &err_msg);
        if (rc != SQLITE_OK) {
            // 이미 데이터가 중복 삽입되는 것을 방지하기 위해 에러 출력을 하되 프로그램은 지속할 수 있습니다.
            fprintf(stderr, "데이터 삽입 시 알림/에러: %s (이미 데이터가 존재할 수 있습니다)\n", err_msg);
            sqlite3_free(err_msg);
        }
    
        // 4. 데이터 검색 시나리오 1: '개발팀' 소속 직원 검색
        printf("=== [검색 1] 개발팀 소속 직원 조회 ===\n");
        const char* sql_select_dev = "SELECT name, skill, experience_years FROM employees WHERE department = '개발팀';";
        rc = sqlite3_exec(db, sql_select_dev, callback, 0, &err_msg);
        if (rc != SQLITE_OK) {
            fprintf(stderr, "검색 실패: %s\n", err_msg);
            sqlite3_free(err_msg);
        }
    
        // 5. 데이터 검색 시나리오 2: 경력 3년 이상인 직원 검색
        printf("\n=== [검색 2] 경력 3년 이상인 직원 조회 ===\n");
        const char* sql_select_exp = "SELECT name, department, experience_years FROM employees WHERE experience_years >= 3;";
        rc = sqlite3_exec(db, sql_select_exp, callback, 0, &err_msg);
        if (rc != SQLITE_OK) {
            fprintf(stderr, "검색 실패: %s\n", err_msg);
            sqlite3_free(err_msg);
        }
    
        // 6. 데이터베이스 연결 종료
        sqlite3_close(db);
        return 0;
    }

<img width="1094" height="609" alt="image" src="https://github.com/user-attachments/assets/20b5acff-37fb-4090-9e56-2bb0adda6e14" />
