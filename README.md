import pygame
import random

# Konstanten für das Spiel
SCREEN_WIDTH = 400
SCREEN_HEIGHT = 600
GRAVITY = 0.25
BIRD_JUMP = 4
PIPE_WIDTH = 70
GAP_HEIGHT = 150
PIPE_DISTANCE = 200
BIRD_WIDTH = 40
BIRD_HEIGHT = 30
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)

# Funktion zum Neustarten des Spiels
def restart_game():
    global bird_y, bird_velocity, pipes
    bird_y = SCREEN_HEIGHT // 2
    bird_velocity = 0
    pipes = []
    create_pipe()

# Funktion zum Erstellen einer neuen Pipe
def create_pipe():
    gap_y = random.randint(50, SCREEN_HEIGHT - GAP_HEIGHT - 50)
    pipes.append({'x': SCREEN_WIDTH, 'y': gap_y})

# Funktion zum Zeichnen des Bildschirms
def draw_screen():
    screen.fill(WHITE)
    pygame.draw.rect(screen, (0, 255, 0), (0, SCREEN_HEIGHT - 50, SCREEN_WIDTH, 50)) # Boden
    pygame.draw.rect(screen, BLACK, (bird_x, bird_y, BIRD_WIDTH, BIRD_HEIGHT)) # Vogel
    for pipe in pipes:
        pygame.draw.rect(screen, (0, 255, 0), (pipe['x'], 0, PIPE_WIDTH, pipe['y'])) # Obere Pipe
        pygame.draw.rect(screen, (0, 255, 0), (pipe['x'], pipe['y'] + GAP_HEIGHT, PIPE_WIDTH, SCREEN_HEIGHT)) # Untere Pipe

# Funktion zum Aktualisieren des Spiels
def update_game():
    global bird_y, bird_velocity
    bird_velocity += GRAVITY
    bird_y += bird_velocity
    if bird_y >= SCREEN_HEIGHT - 50 - BIRD_HEIGHT:
        bird_y = SCREEN_HEIGHT - 50 - BIRD_HEIGHT
        bird_velocity = 0

    for pipe in pipes:
        pipe['x'] -= 3
        if pipe['x'] <= 0:
            pipes.remove(pipe)
            create_pipe()

        if bird_x + BIRD_WIDTH > pipe['x'] and bird_x < pipe['x'] + PIPE_WIDTH:
            if bird_y < pipe['y'] or bird_y + BIRD_HEIGHT > pipe['y'] + GAP_HEIGHT:
                restart_game()

# Initialisierung von Pygame
pygame.init()
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Flappy Bird")
clock = pygame.time.Clock()

# Spielvariablen
bird_x = 50
bird_y = SCREEN_HEIGHT // 2
bird_velocity = 0
pipes = []

# Spielhauptschleife
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                bird_velocity = -BIRD_JUMP

    update_game()
    draw_screen()
    pygame.display.flip()
    clock.tick(60)

pygame.quit()
