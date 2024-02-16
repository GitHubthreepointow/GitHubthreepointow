import pygame
import random

# Initialize Pygame
pygame.init()

# Set screen dimensions
WIDTH, HEIGHT = 600, 400
CELL_SIZE = 20
GRID_WIDTH, GRID_HEIGHT = WIDTH // CELL_SIZE, HEIGHT // CELL_SIZE

# Define colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
GREEN = (0, 255, 0)
RED = (255, 0, 0)

# Set up the screen
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Snake Game")

clock = pygame.time.Clock()

# Snake class
class Snake:
    def __init__(self):
        self.body = [(GRID_WIDTH // 2, GRID_HEIGHT // 2)]
        self.direction = random.choice(["UP", "DOWN", "LEFT", "RIGHT"])

    def move(self):
        head = self.body[0]
        x, y = head
        if self.direction == "UP":
            new_head = (x, y - 1)
        elif self.direction == "DOWN":
            new_head = (x, y + 1)
        elif self.direction == "LEFT":
            new_head = (x - 1, y)
        elif self.direction == "RIGHT":
            new_head = (x + 1, y)
        self.body = [new_head] + self.body[:-1]

    def grow(self):
        tail = self.body[-1]
        self.body.append(tail)

    def draw(self):
        for segment in self.body:
            x, y = segment
            pygame.draw.rect(screen, GREEN, (x * CELL_SIZE, y * CELL_SIZE, CELL_SIZE, CELL_SIZE))

# Food class
class Food:
    def __init__(self):
        self.position = self.randomize_position()

    def randomize_position(self):
        return (random.randint(0, GRID_WIDTH - 1), random.randint(0, GRID_HEIGHT - 1))

    def draw(self):
        x, y = self.position
        pygame.draw.rect(screen, RED, (x * CELL_SIZE, y * CELL_SIZE, CELL_SIZE, CELL_SIZE))

# Main function
def main():
    snake = Snake()
    food = Food()
    running = True

    while running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_UP and snake.direction != "DOWN":
                    snake.direction = "UP"
                elif event.key == pygame.K_DOWN and snake.direction != "UP":
                    snake.direction = "DOWN"
                elif event.key == pygame.K_LEFT and snake.direction != "RIGHT":
                    snake.direction = "LEFT"
                elif event.key == pygame.K_RIGHT and snake.direction != "LEFT":
                    snake.direction = "RIGHT"

        screen.fill(BLACK)

        # Move and draw the snake
        snake.move()
        snake.draw()

        # Check for collision with food
        if snake.body[0] == food.position:
            snake.grow()
            food.position = food.randomize_position()

        # Draw the food
        food.draw()

        # Check for collision with boundaries or itself
        head = snake.body[0]
        if (
            head[0] < 0
            or head[0] >= GRID_WIDTH
            or head[1] < 0
            or head[1] >= GRID_HEIGHT
            or head in snake.body[1:]
        ):
            running = False

        pygame.display.flip()
        clock.tick(10)  # Adjust snake speed

    pygame.quit()

if __name__ == "__main__":
    main()
