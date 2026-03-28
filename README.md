# pallviii
this is my first repository
author pallvi kuamri
import pygame
import random

# Initialize Pygame
pygame.init()

# Constants
WIDTH, HEIGHT = 800, 600
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
PADDLE_WIDTH, PADDLE_HEIGHT = 10, 100
BALL_SIZE = 15

# Set up the game window
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption('Pong Game')

# Game clock
clock = pygame.time.Clock()

# Score
player_score = 0
ai_score = 0

# Paddle and ball classes
class Paddle:
    def __init__(self, x, y):
        self.rect = pygame.Rect(x, y, PADDLE_WIDTH, PADDLE_HEIGHT)

    def move(self, y):
        self.rect.y += y
        self.rect.y = max(min(self.rect.y, HEIGHT - PADDLE_HEIGHT), 0)

class Ball:
    def __init__(self):
        self.rect = pygame.Rect(WIDTH // 2, HEIGHT // 2, BALL_SIZE, BALL_SIZE)
        self.speed_x = random.choice((-5, 5))
        self.speed_y = random.choice((-5, 5))

    def move(self):
        self.rect.x += self.speed_x
        self.rect.y += self.speed_y

        # Bounce off the top and bottom
        if self.rect.top <= 0 or self.rect.bottom >= HEIGHT:
            self.speed_y *= -1

        # Reset the ball if it goes off the screen
        if self.rect.left <= 0 or self.rect.right >= WIDTH:
            self.reset()

    def reset(self):
        self.rect.center = (WIDTH // 2, HEIGHT // 2)
        self.speed_x = random.choice((-5, 5))
        self.speed_y = random.choice((-5, 5))

# Initialize paddles and ball
player_paddle = Paddle(0, HEIGHT // 2 - PADDLE_HEIGHT // 2)
ai_paddle = Paddle(WIDTH - PADDLE_WIDTH, HEIGHT // 2 - PADDLE_HEIGHT // 2)
ball = Ball()

# Main game loop
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    keys = pygame.key.get_pressed()
    if keys[pygame.K_UP]:
        player_paddle.move(-5)
    if keys[pygame.K_DOWN]:
        player_paddle.move(5)

    # AI movement
    if ai_paddle.rect.centery < ball.rect.centery:
        ai_paddle.move(5)
    elif ai_paddle.rect.centery > ball.rect.centery:
        ai_paddle.move(-5)

    ball.move()

    # Paddle collision
    if ball.rect.colliderect(player_paddle.rect) or ball.rect.colliderect(ai_paddle.rect):
        ball.speed_x *= -1

    # Draw everything
    screen.fill(BLACK)
    pygame.draw.rect(screen, WHITE, player_paddle.rect)
    pygame.draw.rect(screen, WHITE, ai_paddle.rect)
    pygame.draw.ellipse(screen, WHITE, ball.rect)

    # Draw the score
    font = pygame.font.Font(None, 74)
    score_text = font.render(f'{player_score}  {ai_score}', True, WHITE)
    screen.blit(score_text, (WIDTH // 4, 10))

    # Update the display
    pygame.display.flip()
    clock.tick(60)

pygame.quit()
