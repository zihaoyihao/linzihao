# linzihao
当前项目
import pygame
import random

# 初始化 Pygame
pygame.init()

# 屏幕尺寸
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600

# 颜色定义
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
GRAY = (128, 128, 128)

# 字体设置
font = pygame.font.Font(None, 36)

# 游戏状态
MENU = 0
GAME = 1
END = 2

# 创建屏幕
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("羊了个羊小游戏")

# 图案类
class Tile:
    def __init__(self, image, x, y):
        self.image = image
        self.x = x
        self.y = y
        self.selected = False

    def draw(self):
        if self.selected:
            pygame.draw.rect(screen, (255, 0, 0), (self.x, self.y, 50, 50), 3)
        screen.blit(self.image, (self.x, self.y))

# 生成图案
def generate_tiles():
    tiles = []
    images = [pygame.image.load(f"image{i}.png") for i in range(1, 10)]
    for i in range(8):
        for j in range(10):
            tile_image = random.choice(images)
            tile = Tile(tile_image, i * 50 + 50, j * 50 + 50)
            tiles.append(tile)
    return tiles

# 检查消除
def check_matches(tiles):
    matches = []
    for tile in tiles:
        if tile.selected:
            matches.append(tile)
    if len(matches) == 2 and matches[0].image == matches[1].image:
        return matches
    else:
        return []

# 游戏主循环
game_state = MENU
timer = 60
tiles = []
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            quit()
        elif event.type == pygame.MOUSEBUTTONDOWN:
            if game_state == MENU:
                game_state = GAME
                tiles = generate_tiles()
            elif game_state == GAME:
                pos = pygame.mouse.get_pos()
                for tile in tiles:
                    if tile.x < pos[0] < tile.x + 50 and tile.y < pos[1] < tile.y + 50:
                        tile.selected = not tile.selected
                matches = check_matches(tiles)
                if matches:
                    for match in matches:
                        tiles.remove(match)
            elif game_state == END:
                game_state = MENU

    if game_state == GAME:
        timer -= 1
        if timer <= 0:
            game_state = END

    screen.fill(WHITE)
    if game_state == MENU:
        menu_text = font.render("点击开始游戏", True, BLACK)
        screen.blit(menu_text, (SCREEN_WIDTH // 2 - 100, SCREEN_HEIGHT // 2))
    elif game_state == GAME:
        for tile in tiles:
            tile.draw()
        timer_text = font.render(f"剩余时间：{timer}", True, BLACK)
        screen.blit(timer_text, (10, 10))
    elif game_state == END:
        end_text = font.render("游戏结束！点击重新开始", True, BLACK)
        screen.blit(end_text, (SCREEN_WIDTH // 2 - 150, SCREEN_HEIGHT // 2))

    pygame.display.update()
