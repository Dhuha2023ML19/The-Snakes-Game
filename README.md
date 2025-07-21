# The-Snakes-Game
import pygame
pygame.init()
import random
import os
pygame.mixer.init()


#Defining colors
white=(255,255,255)
red=(255,0,0)
black=(0,0,0)

#Creating Window
screen_width=1200
screen_height=600
gameWindow=pygame.display.set_mode((screen_width,screen_height)) #a tuple of width and height

#Background image
bgimg=pygame.image.load("background.jpg")
bgimg=pygame.transform.scale(bgimg,(screen_width,screen_height)).convert_alpha()

#Coverpage image
coverpage=pygame.image.load("snakes.jpg")
coverpage=pygame.transform.scale(coverpage,(screen_width,screen_height)).convert_alpha()

#Background Image for Game Over
gameoverr=pygame.image.load("gameover.jpg")
gameoverr=pygame.transform.scale(gameoverr,(screen_width,screen_height)).convert_alpha()

#Game Title
pygame.display.set_caption("Snakes Game")    
pygame.display.update()

clock=pygame.time.Clock()

font=pygame.font.SysFont(None,55) #55 is the font size
def text_screen(text,color,x,y):
    screen_text=font.render(text,True,color)
    gameWindow.blit(screen_text,[x,y])

def plot_snake(gameWindow,color,snk_list,snake_size):
    for x,y in snk_list:
        pygame.draw.rect(gameWindow,black,[x,y,snake_size,snake_size])

def welcome():
    exit_game=False
    while not exit_game:
        gameWindow.fill((230,200,220))
        gameWindow.blit(coverpage,(0,0))
        text_screen("Welcome To The Snakes Game",black,300,250)
        text_screen("Press Space Bar To Play",black,320,300)
        
        
        for event in pygame.event.get():
            if event.type==pygame.QUIT:
                exit_game=True
            if event.type==pygame.KEYDOWN:
                if event.key==pygame.K_SPACE:
                    pygame.mixer.music.load("back.mp3")
                    pygame.mixer.music.play()
                    gameloop()
        pygame.display.update()
        clock.tick(90)

#creating a game loop
def gameloop():
    #Game Specific Variables
    exit_game=False
    game_over=False
    snake_x=45
    snake_y=55
    velocity_x=0
    velocity_y=0
    snake_size=20
    apple_x=random.randint(0,screen_width)
    apple_y=random.randint(0,screen_height)
    snk_list=[]
    snk_length=1
    fps=60
    score=0
    #Check if highscore file exists or not
    if(not os.path.exists("Highscore.txt")):
        with open("Highscore.txt","w")as f:
            f.write("0")


    with open("Highscore.txt","r")as f:
        hiscore=f.read()

    while not exit_game: #the game keeps running until while statement becomes False, then the game will exit
        if game_over:
            with open("Highscore.txt","w")as f:
                f.write(str(hiscore))
            gameWindow.fill(white)
            gameWindow.blit(gameoverr,(0,0))
            text_screen("Game Over! press enter to continue",red,250,250)

            for event in pygame.event.get():
                if event.type==pygame.QUIT:
                    exit_game=True
                
                if event.type==pygame.KEYDOWN:
                    if event.key==pygame.K_RETURN:
                        welcome()
        else:
            for event in pygame.event.get():
                if event.type==pygame.QUIT:
                    exit_game=True
                
                if event.type==pygame.KEYDOWN:
                    if event.key==pygame.K_RIGHT:
                        velocity_x=5
                        velocity_y=0
                    if event.key==pygame.K_LEFT:
                        velocity_x=-5
                        velocity_y=0
                    if event.key==pygame.K_UP:
                        velocity_y=-5
                        velocity_x=0
                    if event.key==pygame.K_DOWN:
                        velocity_y=5
                        velocity_x=0

                    if event.key==pygame.K_q:
                        score+=5

            snake_x+=velocity_x
            snake_y+=velocity_y

            if abs(apple_x-snake_x)<10 and abs(snake_y-apple_y)<10:
                score+=10
                #print("score is",score*10)
                apple_x=random.randint(0,screen_width)
                apple_y=random.randint(0,screen_height)
                snk_length+=5

                if score>int(hiscore):
                    hiscore=score

            gameWindow.fill(white)
            gameWindow.blit(bgimg,(0,0))
            text_screen("Score: "+str(score)+"  Hiscore: "+str(hiscore),red,5,5)
            pygame.draw.rect(gameWindow,red,[apple_x,apple_y,snake_size,snake_size])

            

            head=[]
            head.append(snake_x)
            head.append(snake_y)
            snk_list.append(head)

            if len(snk_list)>snk_length:
                del snk_list[0]

            if head in snk_list[:-1]:
                game_over=True
                
                pygame.mixer.music.load("gameover.mp3")
                pygame.mixer.music.play()


            if snake_x<0 or snake_x>screen_width or snake_y<0 or snake_y>screen_height:
                game_over=True
                
                pygame.mixer.music.load("gameover.mp3")
                pygame.mixer.music.play()

                

            # pygame.draw.rect(gameWindow,black,[snake_x,snake_y,snake_length,snake_width])
            plot_snake(gameWindow,black,snk_list,snake_size)  #Calling the plot_snake function.
        pygame.display.update()
        clock.tick(fps)
     
    
    pygame.quit()
    quit()

welcome()
