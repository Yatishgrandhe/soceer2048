import pygame, random, os, time

pygame.init()
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 500
screen = pygame.display.set_mode([SCREEN_WIDTH, SCREEN_HEIGHT])
clock = pygame.time.Clock()

WHITE = (255,255,255)
BLACK = (0,0,0)
RED = (255,0,0)
GREEN = (0,255,0)
BLUE = (0,0,255)
mixer.init()
font = pygame.font.Font(None, 36)
score = 0
font = pygame.font.SysFont('freesansbold.ttf', 30, bold=True)
score_text = font.render('Score : ' + str(score), True, WHITE)

lives_icon = pygame.image.load('life.png')

def new_fruits(fruit):
    fruit_p = str(fruit + ".png")
    p = pygame.image.load(fruit_p)
    d[fruit] = {
        'img': pygame.transform.scale(p, (50, 50)),
        'x': random.randint(100, 500),
        'y': 800,
        'speed_x': random.randint(-10, 10),
        'speed_y': random.randint(-80, -60),
        'launch': False,
        'u': 0,
        'hit': False,
    }
    if random.random() >= 0.75:
        d[fruit]['launch'] = True
    else:
        d[fruit]['launch'] = False

pygame.mixer.music.load("bomb.mp3")

d = {}

fruits = ['guava', 'watermelon', 'apple', 'banana', 'orange', 'bomb']
for fruit in fruits:
    new_fruits(fruit)

def draw_text(screen, text, size, x, y):
    text_surface = font.render(text, True, WHITE)
    text_rect = text_surface.get_rect()
    text_rect.midtop = (x, y)
    screen.blit(text_surface, text_rect)

def draw_hearts(screen, x, y, hearts, image):
    for i in range(hearts):
        img = pygame.image.load(image)
        img = pygame.transform.scale(img, (50, 50))
        img_rect = img.get_rect()
        img_rect.x = int(x + 35 * i)
        img_rect.y = y
        screen.blit(img, img_rect)

def cross_hearts(x, y):
    x1 = pygame.image.load("x.png")
    x1 = pygame.transform.scale(x1, (100, 100))
    screen.blit(x1, (x, y))

backround = pygame.image.load('back.png')

def game_over(Over, score, done):
    screen.blit(backround, (0, 0))
    if Over:
        font = pygame.font.SysFont('freesansbold.ttf', 30, bold=True)
        score_text = font.render('Score : ' + str(score), True, WHITE)
        screen.blit(score_text, (0, 0))
        done = True
        pygame.mixer.music.load("bomb.mp3")
        pygame.mixer.music.play()
        pygame.quit()
        return
    draw_text(screen, "Score : " + str(score), 30, 250, 400)

waiting = True
text = font.render('Single player', True, RED)
otext = font.render('Double Player', True, RED)
players = 1

while waiting:
    screen.fill(BLACK)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
        if event.type == pygame.MOUSEBUTTONDOWN:
            mouse = pygame.mouse.get_pos()
            if 800 / 2 <= mouse[0] <= 800 / 2 + 140 and 500 / 2 <= mouse[1] <= 500 / 2 + 40:
                players = 2
                waiting = False
            if 400 / 2 <= mouse[0] <= 400 / 2 + 140 and 500 / 2 <= mouse[1] <= 500 / 2 + 40:
                waiting = False
    screen.blit(otext, (800 / 2 + 50, 500 / 2))
    screen.blit(text, (400 / 2 + 50, 500 / 2))
    pygame.display.flip()

pygame.init()

timed = False
secs = ''
t = 0
q = time.time() - t
q = int(q)
ptext = font.render(f'seconds: {secs}', True, WHITE)

while waiting:
    screen.fill(BLACK)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
        if timed == False:
            if event.type == pygame.MOUSEBUTTONDOWN:
                mouse = pygame.mouse.get_pos()
                if 800 / 2 <= mouse[0] <= 800 / 2 + 140 and 500 / 2 <= mouse[1] <= 500 / 2 + 40:
                    timed = True
                if 400 / 2 <= mouse[0] <= 400 / 2 + 140 and 500 / 2 <= mouse[1] <= 500 / 2 + 40:
                    timed = False
                    waiting = False
        if timed:
            if event.type == pygame.KEYDOWN:
                if pygame.K_0 <= event.key <= pygame.K_9:
                    number_pressed = str(event.key - pygame.K_0)
                    secs += number_pressed
                    ptext = font.render(f'seconds: {secs}', True, WHITE)
                elif event.key == pygame.K_RETURN:
                    secs = int(secs)
                    waiting = False
                elif event.key == pygame.K_BACKSPACE:
                    secs = secs[:-1]
                    ptext = font.render(f'seconds: {secs}', True, WHITE)
    if timed == False:
        screen.blit(text, (800 / 2 + 50, 500 / 2))
        screen.blit(otext, (400 / 2 + 50, 500 / 2))
    elif timed == True:
        screen.blit(ptext, (50, 200))
    pygame.display.flip()

scores = []
for i in range(players):
    pygame.init()
    clock = pygame.time.Clock()
    backround = pygame.transform.scale(backround, (800, 500))
    done = False
    Over = False
    player_lives = 3
    score = 0
    count = 0
    FPS = 12
    while not done:
        count=count+1
        if timed==True and count==1:
            t=time.time()
        count=count+1
        if count==1:
            draw_hearts(screen, 690, 5, player_lives, 'life.png')
        screen.blit(backround,(0,0))
        draw_hearts(screen, 690, 5, player_lives, 'life.png')
        screen.blit(score_text, (0, 0))
        if timed==True:
            q=time.time()-t
            q=int(q)
            time_text = font.render('Time : ' + str(secs-q), True, (255, 255, 255))
            screen.blit(time_text, (0, 40))
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                done = True 
        for key, value in d.items():
            if value['launch']:
                value['x'] += value['speed_x']
                value['y'] += value['speed_y']
                value['speed_y'] += (1 * value['u'])
                value['u'] += 1
                if value['y']<=800:
                    screen.blit(value['img'], (value['x'], value['y']))
                else:
                    new_fruits(key)  
                current_position = pygame.mouse.get_pos()
                if not value['hit'] and current_position[0] > value['x'] and current_position[0] < value['x']+60 and current_position[1] > value['y'] and current_position[1] < value['y']+60:
                    if key == 'bomb':
                        player_lives -= 1
                        if player_lives == 0:
                            cross_hearts(690, 15)
                        elif player_lives == 1 :
                            cross_hearts(725, 15)
                        elif player_lives == 2 :
                            cross_hearts(760, 15)
                        pygame.mixer.music.load("bomb.mp3")
                        pygame.mixer.music.play()
                        if player_lives == 0 :
                            Over=True
                            game_over(Over,score,done)
                            done=True
                            break
                        half_fruit_path = "expo.png"
                    else:
                        half_fruit_path = "half" + key + ".png"
                    value['img'] = pygame.image.load(half_fruit_path)
                    value['img']=pygame.transform.scale(value['img'], (100, 100))
                    value['speed_x'] += 10
                    if key != 'bomb' :
                        score += 1
                    font = pygame.font.SysFont('freesansbold.ttf', 30, bold=True)
                    score_text = font.render('Score : ' + str(score), True, (255, 255, 255))
                    value['hit'] = True
            else:
                new_fruits(key) 
            FPS=int(10*int(score/10))
        if timed==True:
            if q==secs:
                Over=True
                pygame.mixer.music.load("bomb.mp3")
                game_over(Over,score,done)
                done=True
                break
        if done==False:
            pygame.display.flip()
        if score<=10:  
            clock.tick(12)
            continue
        clock.tick(FPS+5)
    pygame.quit()
    scores.append(score)
pygame.init()
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 500
font = pygame.font.SysFont('freesansbold.ttf', 80, bold=True)
screen = pygame.display.set_mode([SCREEN_WIDTH, SCREEN_HEIGHT])
score_text = font.render('Score : ' + str(scores[0]), True, (255, 255, 255))
if players == 2:
    if scores[0] > scores[1]:
        winning = font.render('Player 1 has won', True, RED)
    elif scores[1] > scores[0]:
        winning = font.render('Player 2 has won', True, RED)
    else:
        winning = font.render('It is a Draw', True, RED)
    score_text1 = font.render('Score : ' + str(scores[1]), True, (255, 255, 255))
count=0
done=False
while count<10000:
    count=count+1
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            done = True
    screen.blit(score_text, (300,230))
    if players==2:
       screen.blit(score_text1, (300,330))
       screen.blit(winning, (300,430))
    pygame.display.flip()
