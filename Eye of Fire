import pygame as pg
import sys
import math
from pygame import gfxdraw
from random import randint

WIN_SIZE = WIDTH, HEIGHT = 350, 350 #This makes WIN_SIZE a tuple of form (W, H)
COLORS = ['darkred', 'red', 'orange', 'yellow', 'white']
STEPS_BETWEEN_COLORS = 4
PIXEL_SIZE = 2
RADIUS = 90
ANGLE_INCREMENTS = round(math.tau/(math.asin((PIXEL_SIZE*0.5)/RADIUS)))
FIRE_WIDTH = WIDTH // PIXEL_SIZE
FIRE_HEIGHT = HEIGHT // PIXEL_SIZE

class Circle:
    def __init__(self, app):
        self.app = app
        self.coord = self.describe_eye()

    def draw_disc(self):
        pg.draw.circle(self.app.screen, 'black', (WIDTH//2,HEIGHT//2), RADIUS)
    
    def describe_eye(self):
        '''create list of x,y tuples describing outline of circle based on radius and centre of screen'''
        self.circle_coords = []
        for i in range(-ANGLE_INCREMENTS//6, ANGLE_INCREMENTS//6):
             x = round((WIDTH/2 + RADIUS*math.cos((i/ANGLE_INCREMENTS)*math.tau))//PIXEL_SIZE) - 21 #this only works for pixel size = 2
             y = round((HEIGHT/2+RADIUS*math.sin((i/ANGLE_INCREMENTS)*math.tau))//PIXEL_SIZE)
             if (x,y) not in self.circle_coords:
                 self.circle_coords.append((x, y))
        for i in range(-ANGLE_INCREMENTS//6, ANGLE_INCREMENTS//6):
             x = round((WIDTH/2 - RADIUS*math.cos((i/ANGLE_INCREMENTS)*math.tau))//PIXEL_SIZE) + 21 #this only works for pixel size = 2
             y = round((HEIGHT/2 - RADIUS*math.sin((i/ANGLE_INCREMENTS)*math.tau))//PIXEL_SIZE)
             if (x,y) not in self.circle_coords:
                 self.circle_coords.append((x, y))        
        return self.circle_coords

class Circle_Fire:
    def __init__(self, app):
        self.app = app
        self.palette = self.get_palette()
        self.circle_coords = {}
        self.initialise_dict()
        self.array_describing_circle = self.get_circle_fire_array()
        self.main_array = self.array_describing_circle


    def draw_fire(self):
        for y, row in enumerate(self.main_array):
            for x, color_index in enumerate(row):
                if color_index:
                    color = self.palette[color_index]
                    gfxdraw.box(self.app.screen, (x * PIXEL_SIZE, y * PIXEL_SIZE,
                                                  PIXEL_SIZE, PIXEL_SIZE), color)
 
    def do_fire(self):
        for x in range(30, FIRE_WIDTH-30):
            for y in range(8, (FIRE_HEIGHT-8)):
                color_index = self.main_array[y][x]
                if color_index: #i.e. if pixel colour is anything but 0-black
                    rnd = randint(-1,1)
                    rnd2 = randint(-1,1)
                    self.main_array[y - round(math.sin(math.tau*(y/FIRE_HEIGHT)))][((x + rnd) % FIRE_WIDTH)
                                    - round(math.sin(math.tau*(x/FIRE_WIDTH)))] = color_index - rnd % 2 
                else:
                    self.main_array[y][x] = 0
                if self.circle_coords[(x,y)]:
                    self.main_array[y+2*rnd2][x+2*rnd] = len(self.palette) - 1 #lookup to re-draw fire "source" defined in Circle with added small randomisation
        for x in range(1, FIRE_WIDTH):
            for y in range(1, (FIRE_HEIGHT-1)):
                color_index = self.main_array[y][x]
                if color_index: #i.e. if pixel colour is anything but 0-black
                    rnd = randint(-1,1)
                    rnd2 = randint(-1,1)
                    self.main_array[y-rnd][(x + rnd2) % FIRE_WIDTH] = color_index - rnd % 2 #i.e. a chance of selecting a lower color index
                else:
                    self.main_array[y][x] = 0
                if self.circle_coords[(x,y)]:
                      self.main_array[y][x] = len(self.palette) - 1
 
    def initialise_dict(self):
        for x in range(FIRE_WIDTH):
            for y in range(FIRE_HEIGHT):
                self.circle_coords[(x,y)] = False
    
    def get_circle_fire_array(self):
        circle_fire_1 = [[0 for i in range(FIRE_WIDTH)] for j in range(FIRE_HEIGHT)] #creates 2-d array of zeros scaled to PIXEL_SIZE & WIN_SIZE
        for x,y in self.app.circle.coord:
            circle_fire_1[y][x] = len(self.palette) - 1
            self.circle_coords[(x,y)] = True
            
        return circle_fire_1
        
    @staticmethod
    def get_palette():
        palette = [(0, 0, 0)]
        for i, color in enumerate(COLORS[:-1]):
            c1, c2 = color, COLORS[i + 1]
            for step in range(STEPS_BETWEEN_COLORS):
                c = pg.Color(c1).lerp(c2, (step + 0.5) / STEPS_BETWEEN_COLORS)
                palette.append(c)
        palette[1] = (0, 0, 0, 255) #manually setting penultimate colour to black, improves performance and visuals 
        return palette
    
    def draw(self):
        #self.draw_palette()
        self.draw_fire()
    
    def update(self):
        self.do_fire()
        

class App:
    def __init__(self):
        self.screen = pg.display.set_mode(size=WIN_SIZE)
        self.clock = pg.time.Clock()
        self.circle = Circle(self)
        self.circle_fire = Circle_Fire(self)

    def update(self):
        self.clock.tick(60)
        self.circle_fire.update()
        pg.display.set_caption(f'{self.clock.get_fps() :.1f}')

    def draw(self):
        self.screen.fill('black')
        self.circle.draw_disc()
        self.circle_fire.draw()

        pg.display.flip()

    def run(self):
        while True:
            for event in pg.event.get():
                if event.type == pg.QUIT:
                    pg.quit()
                    sys.exit()
            self.update()
            self.draw()
            
if __name__ == '__main__':
    app = App()
    app.run()
