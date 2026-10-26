## 1번문제 답안

## 내가 쓴 프롬포트 :
  - 무지개 블록깨기 게임을 C언어로 작성하시오 (그래픽 raylib 최신 버전 라이브러리 활용), visual studio community 최근버전에서 nuget에서 설치해서 사용하고 싶어. 한글은 절대 사용하지 말것, 

## 코드

  #include "raylib.h"
  #include <stdbool.h>
  
  #define SCREEN_WIDTH 800
  #define SCREEN_HEIGHT 600
  
  #define ROWS 7
  #define COLS 10
  #define BRICK_WIDTH 70
  #define BRICK_HEIGHT 20
  #define BRICK_PADDING 10
  #define OFFSET_X 35
  #define OFFSET_Y 60
  
  typedef struct {
      Rectangle rect;
      bool active;
      Color color;
  } Brick;
  
  typedef struct {
      Vector2 pos;
      Vector2 speed;
      float radius;
      bool active;
  } Ball;
  
  typedef struct {
      Rectangle rect;
      float speed;
  } Paddle;
  
  int main(void) {
      // Initialize window
      InitWindow(SCREEN_WIDTH, SCREEN_HEIGHT, "Rainbow Breakout Game");
      SetTargetFPS(60);
  
      // Rainbow colors definition from top to bottom
      Color rainbowColors[ROWS] = {
          RED,
          ORANGE,
          YELLOW,
          GREEN,
          BLUE,
          DARKBLUE,
          PURPLE
      };
  
      // Initialize bricks
      Brick bricks[ROWS][COLS];
      for (int i = 0; i < ROWS; i++) {
          for (int j = 0; j < COLS; j++) {
              bricks[i][j].rect = (Rectangle){
                  (float)(OFFSET_X + j * (BRICK_WIDTH + BRICK_PADDING)),
                  (float)(OFFSET_Y + i * (BRICK_HEIGHT + BRICK_PADDING)),
                  (float)BRICK_WIDTH,
                  (float)BRICK_HEIGHT
              };
              bricks[i][j].active = true;
              bricks[i][j].color = rainbowColors[i];
          }
      }
  
      // Initialize paddle and ball
      Paddle paddle = { { (SCREEN_WIDTH - 100) / 2.0f, SCREEN_HEIGHT - 40.0f, 100.0f, 15.0f }, 8.0f };
      Ball ball = { { SCREEN_WIDTH / 2.0f, SCREEN_HEIGHT / 2.0f }, { 0.0f, 0.0f }, 8.0f, false };
  
      int score = 0;
      int lives = 3;
      bool gameOver = false;
      bool gameWon = false;
  
      // Main game loop
      while (!WindowShouldClose()) {
          // Update logic
          if (!gameOver && !gameWon) {
              // Paddle movement via Arrow keys or A/D keys
              if (IsKeyDown(KEY_LEFT) || IsKeyDown(KEY_A)) paddle.rect.x -= paddle.speed;
              if (IsKeyDown(KEY_RIGHT) || IsKeyDown(KEY_D)) paddle.rect.x += paddle.speed;
  
              // Screen boundaries for paddle
              if (paddle.rect.x < 0) paddle.rect.x = 0;
              if (paddle.rect.x + paddle.rect.width > SCREEN_WIDTH) {
                  paddle.rect.x = SCREEN_WIDTH - paddle.rect.width;
              }
  
              // Ball launch and movement
              if (!ball.active) {
                  ball.pos.x = paddle.rect.x + paddle.rect.width / 2.0f;
                  ball.pos.y = paddle.rect.y - ball.radius - 2.0f;
  
                  if (IsKeyPressed(KEY_SPACE)) {
                      ball.active = true;
                      ball.speed = (Vector2){ 4.0f, -5.0f };
                  }
              }
              else {
                  ball.pos.x += ball.speed.x;
                  ball.pos.y += ball.speed.y;
  
                  // Wall collisions (Left, Right, Top)
                  if (ball.pos.x - ball.radius < 0) {
                      ball.pos.x = ball.radius;
                      ball.speed.x *= -1.0f;
                  }
                  if (ball.pos.x + ball.radius > SCREEN_WIDTH) {
                      ball.pos.x = SCREEN_WIDTH - ball.radius;
                      ball.speed.x *= -1.0f;
                  }
                  if (ball.pos.y - ball.radius < 0) {
                      ball.pos.y = ball.radius;
                      ball.speed.y *= -1.0f;
                  }
  
                  // Bottom collision (Lose life)
                  if (ball.pos.y + ball.radius > SCREEN_HEIGHT) {
                      lives--;
                      ball.active = false;
                      if (lives <= 0) {
                          gameOver = true;
                      }
                  }
  
                  // Paddle collision
                  if (CheckCollisionCircleRec(ball.pos, ball.radius, paddle.rect)) {
                      ball.speed.y *= -1.0f;
                      // Add angle variance based on where the ball hits the paddle
                      float hitFactor = (ball.pos.x - (paddle.rect.x + paddle.rect.width / 2.0f)) / (paddle.rect.width / 2.0f);
                      ball.speed.x = hitFactor * 6.0f;
                  }
  
                  // Brick collisions
                  bool allCleared = true;
                  for (int i = 0; i < ROWS; i++) {
                      for (int j = 0; j < COLS; j++) {
                          if (bricks[i][j].active) {
                              allCleared = false;
                              if (CheckCollisionCircleRec(ball.pos, ball.radius, bricks[i][j].rect)) {
                                  bricks[i][j].active = false;
                                  ball.speed.y *= -1.0f;
                                  score += (ROWS - i) * 10;
                              }
                          }
                      }
                  }
  
                  if (allCleared) {
                      gameWon = true;
                  }
              }
          }
          else {
              // Restart game on ENTER key
              if (IsKeyPressed(KEY_ENTER)) {
                  score = 0;
                  lives = 3;
                  gameOver = false;
                  gameWon = false;
                  ball.active = false;
                  for (int i = 0; i < ROWS; i++) {
                      for (int j = 0; j < COLS; j++) {
                          bricks[i][j].active = true;
                      }
                  }
              }
          }
  
          // Draw phase
          BeginDrawing();
          ClearBackground(RAYWHITE);
  
          if (!gameOver && !gameWon) {
              // Draw bricks
              for (int i = 0; i < ROWS; i++) {
                  for (int j = 0; j < COLS; j++) {
                      if (bricks[i][j].active) {
                          DrawRectangleRec(bricks[i][j].rect, bricks[i][j].color);
                          DrawRectangleLinesEx(bricks[i][j].rect, 1, Fade(BLACK, 0.2f));
                      }
                  }
              }
  
              // Draw paddle and ball
              DrawRectangleRec(paddle.rect, DARKGRAY);
              DrawCircleV(ball.pos, ball.radius, MAROON);
  
              // Draw UI texts
              DrawText(TextFormat("Score: %d", score), 20, 20, 20, DARKGRAY);
              DrawText(TextFormat("Lives: %d", lives), SCREEN_WIDTH - 120, 20, 20, DARKGRAY);
  
              if (!ball.active) {
                  DrawText("PRESS SPACE TO LAUNCH", SCREEN_WIDTH / 2.0f - 115, SCREEN_HEIGHT / 2.0f + 40, 18, DARKGRAY);
              }
          }
          else if (gameOver) {
              DrawText("GAME OVER", SCREEN_WIDTH / 2.0f - 110, SCREEN_HEIGHT / 2.0f - 40, 40, RED);
              DrawText("PRESS ENTER TO RESTART", SCREEN_WIDTH / 2.0f - 120, SCREEN_HEIGHT / 2.0f + 20, 20, DARKGRAY);
          }
          else if (gameWon) {
              DrawText("YOU WIN!", SCREEN_WIDTH / 2.0f - 95, SCREEN_HEIGHT / 2.0f - 40, 40, GREEN);
              DrawText("PRESS ENTER TO RESTART", SCREEN_WIDTH / 2.0f - 120, SCREEN_HEIGHT / 2.0f + 20, 20, DARKGRAY);
          }
  
          EndDrawing();
      }
  
      CloseWindow();
      return 0;
  }

<img width="794" height="624" alt="image" src="https://github.com/user-attachments/assets/04cba988-02fd-412c-9727-16625dbe83d0" />
<img width="795" height="623" alt="image" src="https://github.com/user-attachments/assets/db1e8779-6b6e-4c41-9dbe-e0adbaeb639c" />
