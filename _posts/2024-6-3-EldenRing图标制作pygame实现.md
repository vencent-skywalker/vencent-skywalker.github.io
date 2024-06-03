## 使用pygame简单画出艾尔登法环的图标

[原图标](https://github.com/vencent-skywalker/file_for_download/blob/main/eldenring_icon1.png)
[pygame作图效果](https://github.com/vencent-skywalker/file_for_download/blob/main/eldenring_icon.png)

```python
import pygame
import math

pygame.init()

# 设置窗口大小和颜色
screen = pygame.display.set_mode((600, 550))
background_color = (25, 33, 86)
screen.fill(background_color)

FPS = 240  # 适当提高帧率以确保动画流畅
clock = pygame.time.Clock() # 保持稳定帧率

# 存储圆周上的点
def draw_circle(x0, y0, radius):  
    # 圆心位置和半径
    center_pos = (300, 280)
    circle_points = []
    for angle in range(360):
        degree = math.radians(angle)
        #这里可以自己定义要画出来的形状
        x = x0 + int(radius * math.cos(degree) + center_pos[0])
        y = y0 + int(radius * math.sin(degree) + center_pos[1])
        circle_points.append((x, y))
    return circle_points

# 事实证明，下面的函数没有必要
def draw_line(line_len):
    begin_pos = (300, 15)
    line_points = []
    for i in range(line_len):
        x = int(begin_pos[0])
        y = int(begin_pos[1] + i)
        line_points.append((x, y))
    return line_points

# 定义自定义事件
DRAW_CIRCLE_EVENT = pygame.USEREVENT + 1
DRAW_LINE_EVENT = pygame.USEREVENT + 1
pygame.time.set_timer(DRAW_CIRCLE_EVENT, 5)  # 设置每5毫秒触发一次事件
pygame.time.set_timer(DRAW_LINE_EVENT, 5)

font1 = pygame.font.SysFont("Microsoft YaHei", 80)
text1 = font1.render("Elden Ring", True, (255, 228, 0))
font1.set_bold(True)
screen.blit(text1, (115, 400))

current_point_index = 0
drawing = True

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            exit()
        elif event.type == DRAW_CIRCLE_EVENT and drawing:
            pygame.draw.circle(screen, (255, 228, 0), draw_circle(-50, 0, 100)[current_point_index], 2)
            pygame.draw.circle(screen, (255, 228, 0), draw_circle(50, 0, 100)[current_point_index], 2)
            pygame.draw.circle(screen, (255, 228, 0), draw_circle(0, -80, 100)[current_point_index], 2)
            pygame.draw.circle(screen, (255, 228, 0), draw_circle(0, -30, 115)[current_point_index], 1.5)
            if 15 < current_point_index < 165:
                #screen.fill((255, 0, 0))
                pygame.draw.circle(screen, (255, 228, 0), draw_circle(0, -860, 600)[current_point_index], 4)
            if 40 < current_point_index < 140:
                #screen.fill((255, 0, 0))
                pygame.draw.circle(screen, (255, 228, 0), draw_circle(0, -220, 240)[current_point_index], 2) 
            if 70 < current_point_index < 110:
                #screen.fill((255, 0, 0))
                pygame.draw.circle(screen, (255, 228, 0), draw_circle(0, -550, 450)[current_point_index], 2) 
            current_point_index += 1

            # 当所有点都绘制完毕后，停止触发事件
            if current_point_index >= 360:
                drawing = False
                pygame.time.set_timer(DRAW_CIRCLE_EVENT, 0)
            pygame.draw.line(screen, (255, 228, 0), (300, 17), (300, 410), 3)

    # 更新屏幕并控制帧率
    pygame.display.update()
    time_passed = clock.tick(FPS)
```