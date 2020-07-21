---
title: A Kinematic Simulator of Autonomous Vehicles in Pygame
date: 2019-06-25 15:54:08
tags:
categories:
  - Programming
  - Python
---

# Pygame Initialization 

在使用**PyGame**模块的所有功能之前，应当使用`pygame.init()`初始化 **PyGame**。<br>
使用`pip`安装好对应版本的**PyGame**， 创建一个新的 `main.py`文件，并输入以下代码初始化 **PyGame**。
```python
import pygame

# For using key value of keyboard
from pygame.locals import *

def main():
    pygame.init()

if __name__ == "__main__":
    main()
```

# Pygame Object Construction

## Screen Object

初始化 **PyGame** 后，第一件事情应该是创建一个屏幕对象用以显示接下来仿真环境所需要的各种对象。在使用面向对象的编程方法中，可以设置一个`Game`的类用于存放 **PyGame** 的各种对象，屏幕对象也以成员变量的方式存放在其中。

```python
class Game:
    def __init__(self):
        pygame.init()
        pygame.display.set_caption("Vehicle Simulation")
        width = 1920
        height = 960
        self.screen = pygame.display.set_mode((width, height))
        self.clock = pygame.time.Clock()
        self.ticks = 30
        self.exit = False

if __name__ == "__main__":
    game = Game()
```

运行上面代码后，可以看到一个黑色的屏幕一闪而过，那是因为代码运行到最后一行便自动结束了程序。因此，我们可以在 `Game`的类中添加函数作为游戏主循环。

## Game Loop

所有的事件或者动作，都发生在游戏主循环内（如刷新屏幕，更新对象状态），因此应当确保有合理的手段退出游戏主循环，否则该循环将一直保持下去。我们可以通过捕获键盘的按键或者关闭窗口的事件来终止游戏主循环。为了捕获事件，我们调用`pygame.event.get()`函数，该函数会返回一个列表，列表内存储了所有发生的事件，我们可以通过轮询该列表然后采取相应的操作。具体**捕获事件并退出游戏主循环**的代码如下所示：

```python
    def run(self):
        # Game loop
        while not self.exit:
            # Set tick
            dt = self.clock.get_time() / 1000
            self.clock.tick(self.ticks)

            # Event queue
            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    self.exit = True
                elif event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_ESCAPE:
                        self.exit = True
	pygame.quit()
```

## Surface and Rect

Surface 和 Rect 对象是 **PyGame** 的基础对象。Surface 对象可以被理解为空白画布，我们在 Surface 对象上填充想要的图案，Screen 其实也是一个 Surface 对象。而 Rect 对象则代表了 Surface 对象
的位置及其包含的区域。在后续的汽车对象中，为了显示及刷新车辆的坐标，我们需要用到 Surface 和 Rect 对象。下面代码是一个简单的例子：
```python
# Create the surface and pass in a tuple with its length and width
surf = pygame.Surface((50, 50))
# Give the surface a color to differentiate it from the background
surf.fill((255, 255, 255))
rect = surf.get_rect()
```

## Blit and Flip

`blit` 和 `flip` 是两个在 **PyGame** 中很重要的方法，`blit` 方法以 **Surface** 和 **位置** 作为输入，将输入的 **Surface** 显示在指定的**位置**上。`pygame.display.flip()` 则是所有在屏幕上的对象，以改变上一次`pygame.display.flip()`显示的内容。
在使用了`blit`之后还需要调用`flip`才能刷新图像，但是使用`blit`看不到想要的结果。
```python
# This line says "Draw surf onto screen at coordinates x:400, y:300"
screen.blit(surf, (400, 300))
pygame.display.flip()
```

# Vehicle Object Construction

## Sprite

**PyGame** 提供了基类`Sprite`用于构建对象在 Screen 上表示 2D 的图像。我们拓展`Sprite`类来构建我们自己的对象 **Vehicle** ，该对象只有 **self.image** (其实就是 **Surface** 对象) 和 **self.rect** 两个成员变量。
```python
class Vehicle(pygame.sprite.Sprite):
    def __init__(self, 
		 image_path):
        # Call Sprite __init__ function
        super(Vehicle, self).__init__()

        # Pygame parameters
        self.image = self.ori_image = pygame.image.load(image_path)  # Use origin image for rotation
        self.rect = self.image.get_rect()
```
有了 **self.image** 和 **self.rect** ，只要加上`blit`和`flip`方法就可以在 Screen 上显示图像。

## Vehicle parameters

为了提供车辆运动学的仿真，我们还需要定义车辆的参数，因为没有涉及到动力学的内容，所以不需要定义与力相关的参数。与阿克曼转向几何相关的参数为车的**轴距**和**最大轮胎转向角度**，因此这两个参数是最重要的参数。同时还会有车辆速度，加速度，角速度等参数。完整的 **Vehicle** 类的成员变量定义如下：

```python
# Vehicle contains parameters of vehicles and provides
# a method to update the status of vehicles.
class Vehicle(pygame.sprite.Sprite):
    def __init__(self, x, y, image_path,
                 angle=0.0,
                 length=4,
                 max_steering=30,
                 max_acceleration=5.0):
        # Call Sprite __init__ function
        super(Vehicle, self).__init__()

        # Pygame parameters
        self.image = self.ori_image = pygame.image.load(image_path)  # Use origin image for rotation
        self.rect = self.image.get_rect()

        # Vehicle parameters
        self.position = Vector2(x, y)
        self.velocity = Vector2(0.0, 0.0)
        self.angle = angle
        self.length = length
        self.max_acceleration = max_acceleration
        self.max_steering = max_steering
        self.max_velocity = 20
        self.brake_deceleration = 10
        self.free_deceleration = 2

        self.acceleration = 0.0
        self.steering = 0.0
```

## Vehicle update logic

在进入一次 Game Loop 中，都会调用一次`update`方法去更新车辆的位置和朝向。
**更新方法：**，通过计算车辆计算加速度与离散时间 dt 的乘积得到车辆速度。然后通过车辆速度，轴距，dt， 根据[**阿克曼几何**](https://sophistt.github.io/2019/06/14/ackman-geometrics-and-pure-pursuit-algorithm/)计算出车辆位置和车头朝向，最后调用`pygame.transform.rotate()`方法将Surface旋转合适的角度。
```python
    def update(self, dt):
        # Logic update
        self.velocity += (self.acceleration * dt, 0)
        self.velocity.x = max(-self.max_velocity, min(self.velocity.x, self.max_velocity))

        if self.steering:
            turning_radius = self.length / tan(radians(self.steering))
            angular_velocity = self.velocity.x / turning_radius
        else:
            angular_velocity = 0

        self.position += self.velocity.rotate(-self.angle) * dt
        self.angle += degrees(angular_velocity) * dt

        # Image update
        self.image = pygame.transform.rotate(self.ori_image, self.angle)
        self.rect = self.image.get_rect()
```

# Draw Line fo Lane

例程构建了一个 1920 * 960 像素的 Screen，同时每 32 个像素代表 1 米的距离，即整个屏幕代表了 60 * 30 的范围。 标准车道宽度为 3.5 米，但是有实线和虚线之分，我们通过调用`pygame.gfxdraw.line()`方法在 Game Loop 中绘制车道线即可。
```python
    # Draw road line
    def drawRoadLine(self):
        for i in range(20):
            pygame.gfxdraw.line(self.screen, 100 * i, 480, 100 * i + 50, 480, (255, 255, 255))
        pygame.gfxdraw.line(self.screen, 0, 368, 1920, 368, (255, 255, 255))
        pygame.gfxdraw.line(self.screen, 0, 592, 1920, 592, (255, 255, 255))
```

# Full Code and Example

## Part of code

将上面的代码整合一下，可以得到完整的`run`函数。
```python
    def run(self):
        ppu = 32  # Pixel per unit

        all_sprites = pygame.sprite.Group()  # Create a new sprite group

        # Get image path
        current_dir = os.path.dirname(os.path.abspath(__file__))
        image_path = os.path.join(current_dir, "image/car.png")
        obstacle_path = os.path.join(current_dir, "image/obstacle.png")

        # Register sprites
        vehicle = Vehicle(2, 16.75, image_path)
        all_sprites.add(vehicle)

        obstacle = Vehicle(22, 16.75, obstacle_path)
        all_sprites.add(obstacle)

        # Pure pursuit for trajectory
        purepursuit = PurePursuit()

        # Game loop
        while not self.exit:
            # Set tick
            dt = self.clock.get_time() / 1000
            self.clock.tick(self.ticks)

            # Event queue
            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    self.exit = True
                elif event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_ESCAPE:
                        self.exit = True
            
            # Set velocity of obstacle
            obstacle.velocity.x = 3
            # Path Plan and purepursuit

            # Compute hermite curve
            curPoint = vehicle.position
            aPoint = PathPlan.searchGoalPoint(curPoint, obstacle)
            path = PathPlan.hermite(curPoint, aPoint, 0)
            
            # Compute angle to avoid obstacles
            vehicle.angle = -purepursuit.trajectoryTracking(path, vehicle.position, vehicle.angle)
            vehicle.acceleration = 3

            # Drawing
            self.screen.fill((0, 0, 0))
            self.drawRoadLine()

            for entity in all_sprites:
                entity.update(dt)  # Update of logic and image
                self.screen.blit(entity.image, entity.position * ppu - (entity.rect.width / 2, entity.rect.height / 2))

            # Draw plan path
            for i in range(len(path)):
                self.screen.set_at([int(ppu * path[i].x), int(ppu * path[i].y)], (255, 255, 255))

            pygame.display.flip()
        pygame.quit()
```
具体效果如下面的 Example 所示。[完整代码](https://github.com/Sophistt/Small_Projects/blob/master/vehicle_simulator/simulation.py)

## Example

<div align="center">
<img height="50%" width="50%" src="https://i.imgur.com/rgfhNGm.gif" title="example"/><br>
gif卡顿是因为录制帧率太低，与PyGame和代码无关。
</div>

# References

- [PyGame – A Primer](https://realpython.com/pygame-a-primer/)
- [pygame 2d car tutorial](http://rmgi.blog/pygame-2d-car-tutorial.html)