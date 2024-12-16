import pygame
import random
import sys

# Initialize pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Sky Warrior")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
BLUE = (0, 0, 255)

# Clock
clock = pygame.time.Clock()
FPS = 60

# Player properties
player_width, player_height = 50, 50
player_x, player_y = WIDTH // 2, HEIGHT - 100
player_speed = 5

# Enemy properties
enemy_width, enemy_height = 50, 50
enemies = []
enemy_speed = 3
enemy_spawn_rate = 30  # Higher means slower spawn

# Bullet properties
bullet_width, bullet_height = 5, 10
bullets = []
bullet_speed = -7

# Score
score = 0
font = pygame.font.SysFont(None, 36)

def draw_player(x, y):
    pygame.draw.rect(screen, BLUE, (x, y, player_width, player_height))

def draw_enemy(enemy):
    pygame.draw.rect(screen, RED, enemy)

def draw_bullet(bullet):
    pygame.draw.rect(screen, WHITE, bullet)

def draw_score():
    score_text = font.render(f"Score: {score}", True, WHITE)
    screen.blit(score_text, (10, 10))

def main():
    global player_x, player_y, score

    running = True
    frame_count = 0

    while running:
        screen.fill(BLACK)
        frame_count += 1

        # Event handling
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()

        # Player movement
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT] and player_x > 0:
            player_x -= player_speed
        if keys[pygame.K_RIGHT] and player_x < WIDTH - player_width:
            player_x += player_speed
        if keys[pygame.K_SPACE]:
            if frame_count % 10 == 0:  # Fire rate control
                bullets.append(pygame.Rect(player_x + player_width // 2, player_y, bullet_width, bullet_height))

        # Spawn enemies
        if frame_count % enemy_spawn_rate == 0:
            enemy_x = random.randint(0, WIDTH - enemy_width)
            enemies.append(pygame.Rect(enemy_x, 0, enemy_width, enemy_height))

        # Move enemies
        for enemy in enemies[:]:
            enemy.y += enemy_speed
            if enemy.y > HEIGHT:
                enemies.remove(enemy)

        # Move bullets
        for bullet in bullets[:]:
            bullet.y += bullet_speed
            if bullet.y < 0:
                bullets.remove(bullet)

        # Check collisions
        for enemy in enemies[:]:
            for bullet in bullets[:]:
                if enemy.colliderect(bullet):
                    enemies.remove(enemy)
                    bullets.remove(bullet)
                    score += 10

        # Check if enemy hits player
        for enemy in enemies:
            if pygame.Rect(player_x, player_y, player_width, player_height).colliderect(enemy):
                running = False

        # Draw everything
        draw_player(player_x, player_y)
        for enemy in enemies:
            draw_enemy(enemy)
        for bullet in bullets:
            draw_bullet(bullet)
        draw_score()

        # Update display
        pygame.display.flip()
        clock.tick(FPS)

    # Game over
    screen.fill(BLACK)
    game_over_text = font.render("Game Over! Press R to Restart", True, WHITE)
    final_score_text = font.render(f"Final Score: {score}", True, WHITE)
    screen.blit(game_over_text, (WIDTH // 2 - game_over_text.get_width() // 2, HEIGHT // 2 - 50))
    screen.blit(final_score_text, (WIDTH // 2 - final_score_text.get_width() // 2, HEIGHT // 2))
    pygame.display.flip()

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN and event.key == pygame.K_r:
                main()

if __name__ == "__main__":
    main()
