
import tkinter as tk
import random

class SnakeGame:
    def __init__(self, root):
        self.root = root
        self.root.title("Snake Game")

        self.width = 600
        self.height = 400
        self.cell_size = 20

        self.canvas = tk.Canvas(root, width=self.width, height=self.height, bg="black")
        self.canvas.pack()

        self.snake = [(self.width // 2, self.height // 2)]
        self.snake_direction = "Right"
        self.food = self.create_food()

        self.game_running = True

        self.root.bind("<KeyPress>", self.change_direction)
        self.update()

    def create_food(self):
        x = random.randint(0, (self.width - self.cell_size) // self.cell_size) * self.cell_size
        y = random.randint(0, (self.height - self.cell_size) // self.cell_size) * self.cell_size
        return (x, y)

    def change_direction(self, event):
        directions = {
            "Up": "Down",
            "Down": "Up",
            "Left": "Right",
            "Right": "Left"
        }
        if event.keysym in directions and directions[event.keysym] != self.snake_direction:
            self.snake_direction = event.keysym

    def move_snake(self):
        head_x, head_y = self.snake[0]
        if self.snake_direction == "Up":
            new_head = (head_x, head_y - self.cell_size)
        elif self.snake_direction == "Down":
            new_head = (head_x, head_y + self.cell_size)
        elif self.snake_direction == "Left":
            new_head = (head_x - self.cell_size, head_y)
        elif self.snake_direction == "Right":
            new_head = (head_x + self.cell_size, head_y)

        self.snake = [new_head] + self.snake[:-1]

    def check_collision(self):
        head_x, head_y = self.snake[0]
        if head_x < 0 or head_x >= self.width or head_y < 0 or head_y >= self.height:
            return True
        if self.snake[0] in self.snake[1:]:
            return True
        return False

    def check_food(self):
        if self.snake[0] == self.food:
            self.snake.append(self.snake[-1])
            self.food = self.create_food()

    def update(self):
        if self.game_running:
            self.move_snake()
            if self.check_collision():
                self.game_running = False
                self.canvas.create_text(self.width // 2, self.height // 2, text="Game Over", fill="red", font=("Helvetica", 24))
            else:
                self.check_food()
                self.canvas.delete(tk.ALL)
                for x, y in self.snake:
                    self.canvas.create_rectangle(x, y, x + self.cell_size, y + self.cell_size, fill="green")
                self.canvas.create_rectangle(self.food[0], self.food[1], self.food[0] + self.cell_size, self.food[1] + self.cell_size, fill="red")
                self.root.after(100, self.update)

if __name__ == "__main__":
    root = tk.Tk()
    game = SnakeGame(root)
    root.mainloop()
