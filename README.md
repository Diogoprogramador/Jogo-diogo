# Jogo-diogo
import pygame
import random

# Inicialização do pygame
pygame.init()

# Configurações da tela
screen_width = 800
screen_height = 600
screen = pygame.display.set_mode((screen_width, screen_height))
pygame.display.set_caption("Jogo Diogo")

# Cores
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
BLUE = (0, 0, 255)

# Variáveis do jogo
clock = pygame.time.Clock()
running = True
game_over = False
score = 0

# Paddle
paddle_width = 100
paddle_height = 10
paddle = pygame.Rect(screen_width // 2 - paddle_width // 2, screen_height - 50, paddle_width, paddle_height)
paddle_speed = 10

# Bola
ball_radius = 10
ball = pygame.Rect(screen_width // 2, screen_height // 2, ball_radius * 2, ball_radius * 2)
ball_speed_x = 5 * random.choice((1, -1))
ball_speed_y = -5

# Tijolos
brick_width = 75
brick_height = 20
bricks = [pygame.Rect(i * (brick_width + 10) + 35, j * (brick_height + 10) + 35, brick_width, brick_height)
          for i in range(8) for j in range(5)]

# Função para desenhar todos os elementos na tela
def draw():
    screen.fill(BLACK)
    pygame.draw.rect(screen, BLUE, paddle)
    pygame.draw.ellipse(screen, WHITE, ball)
    for brick in bricks:
        pygame.draw.rect(screen, RED, brick)
    font = pygame.font.Font(None, 36)
    score_text = font.render(f"Score: {score}", True, WHITE)
    screen.blit(score_text, (10, 10))
    if game_over:
        game_over_text = font.render("Game Over! Press R to Restart", True, WHITE)
        screen.blit(game_over_text, (screen_width // 2 - 150, screen_height // 2))
    pygame.display.flip()

# Loop principal do jogo
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if game_over and event.type == pygame.KEYDOWN:
            if event.key == pygame.K_r:
                ball.x, ball.y = screen_width // 2, screen_height // 2
                ball_speed_x = 5 * random.choice((1, -1))
                ball_speed_y = -5
                paddle.x = screen_width // 2 - paddle_width // 2
                bricks = [pygame.Rect(i * (brick_width + 10) + 35, j * (brick_height + 10) + 35, brick_width, brick_height)
                          for i in range(8) for j in range(5)]
                score = 0
                game_over = False

    if not game_over:
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT] and paddle.left > 0:
            paddle.x -= paddle_speed
        if keys[pygame.K_RIGHT] and paddle.right < screen_width:
            paddle.x += paddle_speed

        ball.x += ball_speed_x
        ball.y += ball_speed_y

        if ball.left <= 0 or ball.right >= screen_width:
            ball_speed_x *= -1
        if ball.top <= 0:
            ball_speed_y *= -1

        if ball.colliderect(paddle):
            ball_speed_y *= -1

        for brick in bricks[:]:
            if ball.colliderect(brick):
                ball_speed_y *= -1
                bricks.remove(brick)
                score += 1
                break

        if ball.bottom >= screen_height:
            game_over = True

    draw()
    clock.tick(60)

pygame.quit()
