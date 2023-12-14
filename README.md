import pygame
import sys
import random

# Inicialización de Pygame
pygame.init()

# Configuración del juego
width, height = 640, 480
cell_size = 20
fps = 15

# Colores
black = (0, 0, 0)
white = (255, 255, 255)
red = (255, 0, 0)

# Direcciones posibles
directions = [(0, -1), (0, 1), (-1, 0), (1, 0)]

# Clase Snake
class Snake:
    def __init__(self):
        self.body = [(width // 2, height // 2)]
        self.direction = random.choice(directions)

    def move(self):
        current_head = self.body[0]
        new_head = (current_head[0] + self.direction[0] * cell_size, current_head[1] + self.direction[1] * cell_size)
        self.body.insert(0, new_head)

    def grow(self):
        tail = self.body[-1]
        self.body.append(tail)

    def check_collision(self):
        head = self.body[0]
        return head in self.body[1:]

    def check_boundary(self):
        head = self.body[0]
        return not (0 <= head[0] < width and 0 <= head[1] < height)

    def draw(self, screen):
        for segment in self.body:
            pygame.draw.rect(screen, white, (*segment, cell_size, cell_size))

# Clase Fruit
class Fruit:
    def __init__(self):
        self.position = self.generate_position()

    def generate_position(self):
        x = random.randrange(0, width, cell_size)
        y = random.randrange(0, height, cell_size)
        return x, y

    def draw(self, screen):
        pygame.draw.rect(screen, red, (*self.position, cell_size, cell_size))

# Inicialización del juego
screen = pygame.display.set_mode((width, height))
pygame.display.set_caption("Snake Game")
clock = pygame.time.Clock()

snake = Snake()
fruit = Fruit()

# Bucle principal del juego
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_UP and snake.direction != (0, 1):
                snake.direction = (0, -1)
            elif event.key == pygame.K_DOWN and snake.direction != (0, -1):
                snake.direction = (0, 1)
            elif event.key == pygame.K_LEFT and snake.direction != (1, 0):
                snake.direction = (-1, 0)
            elif event.key == pygame.K_RIGHT and snake.direction != (-1, 0):
                snake.direction = (1, 0)

    # Mover la serpiente
    snake.move()

    # Verificar colisiones
    if snake.check_collision() or snake.check_boundary():
        pygame.quit()
        sys.exit()

    # Verificar si la serpiente ha comido la fruta
    if snake.body[0] == fruit.position:
        snake.grow()
        fruit.position = fruit.generate_position()

    # Limpiar la pantalla
    screen.fill(black)

    # Dibujar la serpiente y la fruta
    snake.draw(screen)
    fruit.draw(screen)

    # Actualizar la pantalla
    pygame.display.flip()

    # Controlar la velocidad del juego
    clock.tick(fps)
