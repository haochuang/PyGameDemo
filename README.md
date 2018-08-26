# PyGameDemo
a demo test for python game

PyGame – A Primer：https://realpython.com/pygame-a-primer/#.Vo0sHqOmRRI.reddit

PyGame是一个用Python写的SDL库。SDL是一个能访问计算机多媒体硬件组件(包括声卡，视频卡，输入组件等)的跨平台库。SDL是一个非常强大的工具，擅长创建基于多媒体硬件的程序，但它是用C语言写的，可C语言很难，所以我们选择PyGame。

在这篇教程中，我们会涉及到PyGame遊戲逻辑，碰撞检测，构建游戏界面和调用第三方库文件到游戏中来。

注意： 本教程已认为读者对Python语法，文件结构和面向对象的有已经基本理解。

## 安装

先前往PyGame下载页面，根据你系统版本和Python版本找到适合的二进制包。如果你使用Python 3，必须确保你下载的PyGame版本是1.9.2的。

新建一个工程，然后在工程项目中，新建一个Python文件，复制以下代码到新建的Python文件中：

```
import pygame
from pygame.locals import *

pygame.init()
```

正如所有Python程序一样，我们必须先导入模块，然后我们才能使用这些模块。正因为如此，在文件开头，我们先导入了pygame模块和pygame.locals模块，然后我们可以从模块中使用已编写好的常量。最后一行是初始化代码，它会初始化所有PyGame模块，你必须先使用这个方法，否则你不能用PyGame做任何事情。


创建游戏组件

Screen对象

首先，我们必须画一些东西，所以我们将创建一个“屏幕”，这个屏幕就是我们的画布。为了能创建一个显示屏幕，我们必须使用pygame.display模块中的set_mode()方法，然后传入一个由窗体高度和窗体宽度组成的元组。(我们设置窗体尺寸为800x600)。

```
import pygame
from pygame.locals import *

pygame.init()
screen = pygame.display.set_mode((800, 600))
```

如果你现在运行代码，会看见一个窗体而且它会突然消失，就像退出程序了一样。不是很爽，是吗？接下来，我们将解释我们的游戏主循环，它会确保我们只有输入正确的退出指令后，程序才会停止运行并退出。

游戏主循环

游戏主循环(事件)是一直在运行着的。无论是在玩游戏，更新游戏状态，渲染画面或者是采集输入数据，它是一直在不间断运行着的。当我们创建一个循环时，必须能保证，我们有方法可以跳出循环，结束程序运行。最后，我们将介绍一些在玩游戏时的基本操作。所有的操作(我们稍后会讲解一些事件内容)都会进过PyGame的事件队列中，你可以用pygame.event.get()方法来操作它。这个方法会返回一个列表，该列表包含队列中所有事件的列表。我们可以对它做个循环遍历，根据事件类型，做出相应的操作。现在我们只关心KEYDOWN(按键)和QUIT(退出)事件。


```
# Variable to keep our main loop running
running = True

# Our main loop!
while running:
   # for loop through the event queue
   for event in pygame.event.get():
       # Check for KEYDOWN event; KEYDOWN is a constant defined in pygame.locals, which we imported earlier
       if event.type == KEYDOWN:
           # If the Esc key has been pressed set running to false to exit the main loop
           if event.key == K_ESCAPE:
               running = False
       # Check for QUIT event; if QUIT, set running to false
       elif event.type == QUIT:
           running = False
```

把以上代码加入之前写的代码中，然后运行它。你应该会看见一个空窗体。除非你看ESC键或者你触发了QUIT事件，否则它是不会关闭窗体并且停止运行的。

面和方块

在PyGame中，面和方块是游戏基础的组件。你可以认为面是一张白纸，你可以在上面想怎么画就怎么画。我们的screen对象其实也是一个面组件。它们可以对图片进行操作。方块代表了一个被面组件包围的矩形面积。

现在开始建一个50像素*50像素的平面，然后填入一个颜色. 我们选择用白色填入平面，因为默认窗体是黑色的并且我们想让窗体明亮可见。我们会在平面上使用get_rect()方法来获得面面积和X，Y坐标。

```
# Create the surface and pass in a tuple with its length and width
surf = pygame.Surface((50, 50))

# Give the surface a color to differentiate it from the background
surf.fill((255, 255, 255))
rect = surf.get_rect()
```

位移和翻转

如果只在窗体中建个面组件是远远不够的，所以我们接下来要做的是，能让面组件动起来。从专业上来说，位移其实就是绘图，就是两个面之间进行位移。请不要忘记哦，我们的屏幕就是另一个面对象。下面代码将演示如何绘制屏幕。


```
# This line says "Draw surf onto screen at coordinates x:400, y:300"
screen.blit(surf, (400, 300))
pygame.display.flip()
```

blit()方法带有两个参数：面对象和坐标。现在我们想绘制在屏幕中央，可当你运行代码的时候，你会发现面并不是显示在屏幕中央。那是因为blit()方法是以窗口左上角为原点坐标。

注意：如果小方块进行了位移，那么请记得使用pygame.display.filp()方法。该方法会更新整个屏幕，绘制出新小方块。如果你不使用该方法，你将看不到任何图像更新。

精灵

什么是精灵？在编程定义中，精灵表示屏幕中的2D元件。从本质上来说，精灵就是一个图像对象。PyGame提供了一个精灵类，它能扩展一个对象来表示一个或多个图形。我们会继承这个类，这样我们可以用它自带的很多方法了。我们会新建一个新对象Player。Player对象会继承精灵类，并且现在它仅有两个属性：面和小方块。我们还会给面属性一个颜色(白色)。现在Player也拥有了一个面。

译者注：Player对象代表了玩家控制的小方块。


```
class Player(pygame.sprite.Sprite):
   def __init__(self):
       super(Player, self).__init__()
   self.surf = pygame.Surface((75, 25))
   self.surf.fill((255, 255, 255))
   self.rect = self.surf.get_rect()
```

现在把代码放在一起：


```
# import the pygame module
import pygame

# import pygame.locals for easier access to key coordinates
from pygame.locals import *

# Define our player object and call super to give it all the properties and methods of pygame.sprite.Sprite
# The surface we draw on the screen is now a property of ‘player’
class Player(pygame.sprite.Sprite):
   def __init__(self):
   super(Player, self).___init__()
   self.surf = pygame.Surface((75, 25))
   self.surf.fill((255, 255, 255))
   self.rect = self.surf.get_rect()

# initialize pygame
pygame.init()

# create the screen object
# here we pass it a size of 800x600
screen = pygame.display.set_mode((800, 600))

# instantiate our player; right now he’s just a rectangle
player = Player()

# Variable to keep our main loop running
running = True

# Our main loop!
while running:
   # for loop through the event queue
   for event in pygame.event.get():
   # Check for KEYDOWN event; KEYDOWN is a constant defined in pygame.locals, which we imported earlier
   if event.type == KEYDOWN:
     # If the Esc key has been pressed set running to false to exit the main loop
     if event.key == K_ESCAPE:
         running = False
     # Check for QUIT event; if QUIT, set running to false
      elif event.type == QUIT:
         running = False
    
    # Draw the player to the screen
    screen.blit(player.surf, (400, 300))
    
    # Update the display
    pygame.display.flip()
```

运行以上代码，你应该会在屏幕中央看见一个白色小方块。

如果把screen.blit(player.surf,(400,300))改成screen.blit(player.surf,player.rect)，你认为会发生什么事情呢？如果真想改成这样，那试着输出player.rect的值到控制台上。rect()的前两个属性分别是rect()左上角的x,y坐标值。当你移动一个小方块时，PyGame会用这些坐标值去绘制一个面。这就是我们接下来让player可以动起来的原因。

## 用户输入

用户输入

现在才开始有趣起来！现在，让我们可以控制player对象。正如我们前面所讨论的一样，keydown(按键)事件pygame.event.get()会提取事件栈中最新的事件。PyGame还有另一个方法叫做pygame.event.get_pressed()。 这个get_pressed()方法会返回一个字典，该字典包含队列中所有keydown事件。我们将它放在游戏主循环中，这样可以获得我们一切想要的。


```
pressed_keys = pygame.event.get_pressed()
```


我们现在要写一个方法，该方法会从按键事件字典中提取按键事件，并且同时会判断精灵行为，然后根据按键的键值，进行相关的操作。


```
def update(self, pressed_keys):
   if pressed_keys[K_UP]:
   self.rect.move_ip(0, -5)
   if pressed_keys[K_DOWN]:
   self.rect.move_ip(0, 5)
   if pressed_keys[K_LEFT]:
   self.rect.move_ip(-5, 0)
   if pressed_keys[K_RIGHT]:
   self.rect.move_ip(5, 0)
```

K_UP, K_DOWN, K_LEFT和K_RIGHT分别代表了键盘上的方向键。如果有方向键按下被检测到，那么就会让rect()进行相关的移动。矩形自带来两个移动方法。在这里，我们使用“移动到位”方法-move_ip()。因为我们不想复制一个新的矩形对象，只是想移动现存的矩形对象。

请添加以下代码到Player类中，并且把get_pressed()方法放进游戏主循环中。所以我们现在的代码应该看上去像这样子：


```
import pygame
from pygame.locals.import *

class Player(pygame.sprite.Sprite):
   def __init__(self):
   super(Player, self).___init__()
   self.surf = pygame.Surface((75, 25))
   self.surf.fill((255, 255, 255))
   self.rect = self.surf.get_rect()
  
    def update(self, pressed_keys):
       if pressed_keys[K_UP]:
      self.rect.move_ip(0, -5)
       if pressed_keys[K_DOWN]:
      self.rect.move_ip(0, 5)
       if pressed_keys[K_LEFT]:
      self.rect.move_ip(-5, 0)
       if pressed_keys[K_RIGHT]:
      self.rect.move_ip(5, 0)

     pygame.init()
     
     screen = pygame.display.set_mode((800, 600))
     
     play = Player()
     running = True
  
     while running:
    for event in pygame.event.get():
        if event.type == KEYDOWN:
       if event.key == K_ESCAPE:
           running = False
             elif event.type = QUIT:
           running = False
         
         pressed_keys = pygame.key.get_pressed()
         player.update(pressed_keys)
         
         screen.blit(player.surf, (400, 300))
         pygame.display.flip()
```

现在，你应该能用方向键来控制屏幕上小方块移动了。你可能会发现，小方块可以移出屏幕，但这不是我们想看到的。现在需要修改update()方法，让它更有逻辑性。如果小方块移出800*600窗体边界的话，它只能紧贴着边界。


```
def update(self, pressed_keys):
   if pressed_keys[K_UP]:
       self.rect.move_ip(0, -5)
   if pressed_keys[K_DOWN]:
       self.rect.move_ip(0, 5)
   if pressed_keys[K_LEFT]:
       self.rect.move_ip(-5, 0)
   if pressed_keys[K_RIGHT]:
       self.rect.move_ip(5, 0)

   # Keep player on the screen
   if self.rect.left < 0:
       self.rect.left = 0
   elif self.rect.right > 800:
        self.rect.right = 800
   if self.rect.top <= 0:
        self.rect.top = 0
   elif self.rect.bottom >= 600:
        self.rect.top = 600
```

如果超过了边界，我们就修改顶部、底部、左边和右边的坐标值。

现在让我们添加一些障碍物。

首先，让我们新建一个“Enemy”类，就如我们创建“Player”类一样，它也是继承自精灵类：


```
class Enemy(pygame.sprite.Sprite):
  def __init__(self):
   super(Enemy, self).__init__()
   self.surf = pygame.Surface((20, 10))
       self.surf.fill((255, 255, 255))
   self.rect = self.surf.get_rect(center=(820, random.randint(0, 600)))
   self.speed = random.randint(5, 20)

   def update(self):
   self.rect.move_ip(-self.speed, 0)
   if self.rect.right < 0:
       self.kill()
```

这里有几个不同地方，我们需要说明下。首先，当我们在面上使用get_rect()方法时，我们设置了它x坐标为820，而y坐标则是用random.randint()方法生成一个随机整数。
Random是一个python库，我们会在文件开头就导入random库。为什么要随机生成整数？这是非常简单的道理：我们想让这些障碍物从屏幕右边(820),但以不同的高度(0-600)飞入。我们也会用random函数来设置这些障碍物的飞行速度。这样可以造成有些障碍物飞得快有些障碍物飞得慢的效果。

在enemy类中的update()方法，不带有任何参数(我们不考虑用户输入)，只是简单恒速从屏幕右边飞到左边。在update()方法中，if条件会检测障碍物的左边界是否越过了屏幕的左边界或者小方块的右边界(它们不是一接触到就立马消失不见)。当它们越过以上两个边界时，我们会使用自带方法kill()，把它们从精灵组中删除掉，以防止它们继续在屏幕上显示。用这个方法并不会释放被占用或者没被引用的内存空间，但Python垃圾回收机制会帮你搞定它们。


群

PyGame提供了另一个非常有用的对象，它就是精灵群。它能做到犹如它名字一样 - 精灵的群。为什么我们不使用一个列表，而是使用精灵群呢？因为精灵群已经提供了很多内置方法，这些方法能帮助我们进行的冲撞和更新。让我们现在新建一个群类，把精灵类都放进一个群类中。创建之后，我们会把Player类和Enemy类分别加进一个群类中。当使用精灵类中kill()方法时，精灵类会被从群类中移除。下面是一小部分代码：


```
enemies = pygame.sprite.Group()
all_sprites = pygame.sprite.Group()
all_sprites.add(player)
```

现在，我们已经有了all_sprites的群对象，现在我们会改动一点代码，能促使它渲染出该群里面所有的对象。


```
for entity in all_sprites:
   screen.blit(entity.surf, entity.rect)
```

现在，任何放进all_sprites群里的对象，都被会渲染出来。


自定义事件

虽然障碍物有自己的精灵群，但事实上，还没有任何障碍物存在。问题来了，如何能使我们在屏幕上显示障碍物？。刚开始游戏时，我们可以在屏幕上新建一些障碍物，但过一会后，我们游戏就没有障碍物了。因此，我们要新建一个自定义事件，它会每几秒新建一些障碍物出来。PyGame会监听这个事件，就像它监听按键和退出事件一样。创建一个自定义事件非常容易，就像它名字一样：


```
ADDENEMY = paygame.USEREVENT + 1
```

就像这样！现在我们有了一个新事件，叫做ADDENEMY。我们在游戏主循环中监听它。需要注意的是必须使自定义事件有一个独一无二的值，并且这个值还要大于USEREVENT的值。这就是为什么我们设事件值等于USEREVENT + 1。 友情小提示：这些值都是整型常数。USEREVENT有一个数字型值，并且任何自定义事件必须是大于USEREVENT值的整数型值(因为所有小于USEREVENT的值都已经被自定义方法分配完了)。

我们有了一个自定义方法。我们必须把它插入事件队列中。为了保证在整个游戏进行时，这个事件一直是在运行的，所以须要设置一个定时器。我们可以用PyGame提供的time()对象做到这点。


```
pygame.time.set_timer(ADDENEMY,250)
```

这行代码会告诉PyGame：每250毫秒触发一次ADDENEMY事件。虽然这个代码不在我们的游戏主循环中，但它仍然可以通过游戏运行。现在增加一些监听代码到我们事件中：


```
while running:
   for event in pygame.event.get():
   if event.type == KEYDOWN:
      if event.key == K_ESCAPE:
          running = False
      elif event.type == QUIT:
               running = False
        elif (event.type == ADDENEMY):
           new_enemy = Enemy()
           enemies.add(new_enemy)
           all_sprites.add(new_enemy)
```

需要注意的是set_time()方法只是负责把事件传入PyGame的事件队列中。

现在，我们可以监听我们的ADDENEMY事件了，当它被触发运行时候，我们会创建一个新障碍物。新建之后，我们会把障碍物添加到精灵群中(稍后我们将用它进行碰撞环节)和all_sprites群中(这样它就可以在屏幕上显示出来).


碰撞

碰撞正是PyGame的魅力所在。虽然写碰撞代码非常困难，但PyGame有很多检测碰撞方法。本教程中，我们将使用spritecollideany()方法。在精灵群中，如果一个精灵对象和另一个精灵对象有碰撞，那么spritecollideany()方法会用一个精灵对象和精灵群对象进行操作。如果我们Player被一个障碍物撞到，那么我们就用Player精灵和障碍物精灵群进行操作。代码如下所示：


```
if pygame.sprite.spritecollideany(player, enemies):
   player.kill()
```

我们会测试在障碍物精灵群中，一个Player精灵对象被任何一个障碍物精灵组有接触。如果有，因为我们只渲染all_sprites组中的精灵，所以我们就使用Player精灵中的kill()方法。与此同时，kill()方法会从这个组中移出一个Player精灵对象。这样我们Player精灵就不会再被渲染了，这就意味着“被移出”了。现在我们把所有代码放在一起把！


```
# import the pygame module
import pygame

# import random for random numbers!
import random

# import pygame.locals for easier access to key coordinates
from pygame.locals import *

class Player(pygame.sprite.Sprite):
  def __init__(self):
   super(Player, self).__init__()
   self.surf = pygame.Surface((75, 25))
   self.surf.fill((255, 255, 255))
   self.rect = self.surf.get_rect()
 
  def update(self, pressed_keys):
   if pressed_keys[K_UP]:
      self.rect.move_ip(0, -5)
   if pressed_keys[K_DOWN]:
      self.rect.move_ip(0, 5)
   if pressed_keys[K_LEFT]:
      self.rect.move_ip(-5, 0)
   if pressed_keys[K_RIGHT]:
      self.rect.move_ip(5, 0)

   # Keep player on the screen
   if self.rect.left < 0:
      self.rect.left = 0
    elif self.rect.right > 800:
      self.rect.right = 800
   if self.rect.top <=0:
      self.rect.top = 0
   elif self.rect.bottom >= 600:
      self.rect.bottom = 600
```

见证奇迹时刻到了！



图片

现在我们已经做好了一个游戏，虽然是个非常难看的游戏。接下来，我们要用一些漂亮的图片来替换那些无聊的小方块。这样，这个游戏看上去就像一个真正的游戏了。

在之前代码中，我们使用白色的面对象。虽然白色能让我们了解面和及其工作原理，但它使游戏变得非常丑陋。我们将为障碍物和小方块添加几张图片。我喜欢画自己的图片，所以我画了一个小飞机来表示小方块和画了一些导弹来表示障碍物。你可以下载从repo中下载到这些代码。你可以随意用我的图片，或者用你自己画的图片，又或者你可以去下载一些免费的图片。

修改对象构造器

我们现在Player构造器看上去是这样子的：


```
class Player(pygame.sprite.Sprite):
   def __init__(self):
       super(Player, self).__init__()
       self.surf = pygame.Surface((75, 25))
       self.surf.fill((255, 255, 255))
       self.rect = self.surf.get_rect()
```

我们新Player构造器看上去是这样的：


```
class Player(pygame.sprite.Sprite):
   def __init__(self):
       super(Player, self).__init__()
       self.image = pygame.image.load('jet.png').convert()
       self.image.set_colorkey((255, 255, 255), RLEACCEL)
       self.rect = self.image.get_rect()
```

我们想用图片来替换我们的Surface对象。我们可以通过传入图片地址到pygame.image.load()方法中。load()方法会返回一个Surface对象。当我们在Surface对象中使用convert()方法，该方法会复制一个Surface对象，这样子在屏幕上会显示流畅一点。

接下来，我们会对图片使用set_colorkey()方法。set_colorkey方法会让设置的图片颜色变得更透明一些。在本教程中，我选择白色，因为它是飞机图片的背景色。RLEACCEL是个可选参数，它能帮助PyGame在非加速显示器中渲染的更快。
最后，在我们图片中使用get_rect()方法，获得rect对象。
请不要忘记，图片仍旧是一个surface对象；它只是现在用图片来表示。

我们如法炮制对enemy构造器，进行修改：


```
class Enemy(pygame.sprite.Sprite):
   def __init__(self):
       super(Enemy, self).__init__()
       self.image = pygame.image.load('missile.png').convert()
       self.image.set_colorkey((255, 255, 255), RLEACCEL)
       self.rect = self.image.get_rect(
           center=(random.randint(820, 900), random.randint(0, 600))
       )
       self.speed = random.randint(5,20)
```

我们现在有了一个比以前更好看的游戏了，尽管我觉得还是缺少了点什么。现在在天空中添加一些云，我们会用相同的方法来做到让飞机表现出穿过云的效果一样。首先，我们新建一个Cloud对象，它带有一张云图片和一个能表现出云从左边移动右边的update方法。然后我们创建一个自定义事件，该事件会一定间隔内创建一个Cloud对象，并添加至all_sprites群中。下面就是我们Cloud对象代码：


```
class Cloud(pygame.sprite.Sprite):
   def __init__(self):
       super(Cloud, self).__init__()
       self.image = pygame.image.load('cloud.png').convert()
       self.image.set_colorkey((0, 0, 0), RLEACCEL)
       self.rect = self.image.get_rect(
           center=(random.randint(820, 900), random.randint(0, 600))
       )

   def update(self):
       self.rect.move_ip(-5, 0)
       if self.rect.right < 0:
           self.kill()
```

新增代码看上去都差不多，现在我们把新增代码放在ADDENEMY自定义事件下面。


```
ADDCLOUD = pygame.USEREVENT + 2
pygame.time.set_timer(ADDCLOUD, 1000)
```

现在新建一个云精灵组：


```
clouds = pygame.sprite.Group()
```

在我们游戏主循环中，我们应该把ADDCLOUD事件加进去。

加入之前：


```
for event in pygame.event.get():
   if event.type == KEYDOWN:
       if event.key == K_ESCAPE:
           running = False
   elif event.type == QUIT:
       running = False
   elif event.type == ADDENEMY:
       new_enemy = Enemy()
       enemies.add(new_enemy)
       all_sprites.add(new_enemy)
```

加入之后：


```
for event in pygame.event.get():
   if event.type == KEYDOWN:
       if event.key == K_ESCAPE:
           running = False
   elif event.type == QUIT:
       running = False
   elif event.type == ADDENEMY:
       new_enemy = Enemy()
       enemies.add(new_enemy)
       all_sprites.add(new_enemy)
   elif event.type == ADDCLOUD:
       new_cloud = Cloud()
       all_sprites.add(new_cloud)
       clouds.add(new_cloud)
```

我们会同时添加Cloud精灵到all_sprites群和Cloud群中。因为我们用all_sprites方法渲染出云，然后再用clouds的update方法。你可能会问为什么我们把它们添加到障碍物群中？毕竟，它们几乎有相同的update方法。原因在于飞机只是穿过云而已，我们不想让飞机与云进行接触，剩下的就是用Cloud Group的update方法了。

结论

就这些！如果你测试成功的话，应该看到以下这样子：

完整代码参考：

https://github.com/haochuang/pygame-primer

读读教程，希望能从中学到点东西.



