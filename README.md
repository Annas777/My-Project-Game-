# My-Project-Game-
###infernal_world.py ###

import pygame
import sys
import os
from pygame.locals import QUIT,KEYDOWN, K_a, K_d , K_w, K_s, K_KP8, K_KP5, K_KP4, K_KP6,\
                               K_LCTRL, K_RCTRL, K_LSHIFT, K_RALT
pygame.font.init()

#Colours
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
LAWNGREEN = (124,252,0)
GREEN = (0, 205, 0)
ORCHID = (255,131,250) #green dragon bullet colour
CYAN = (0,238,238) # yellow dragon bullet colour

VEL = 5
BULLET_VEL_SMALL = 6
BULLET_VEL_BIG = 4
MAX_BULLETS_SMALL = 2
MAX_BULLETS_BIG = 1
BORDER = pygame.Rect(900//2-0.5, 0, 0.5, 500)

HEALTH_FONT = pygame.font.SysFont('comicsans', 40)
WINNER_FONT = pygame.font.SysFont('comicsans', 100)

green_bullets_small = []
yellow_bullets_small = []

green_bullets_big = []
yellow_bullets_big = []



#Create new events
YELLOW_HIT_SMALL = pygame.USEREVENT + 1
GREEN_HIT_SMALL = pygame.USEREVENT + 2

YELLOW_HIT_BIG = pygame.USEREVENT + 3
GREEN_HIT_BIG = pygame.USEREVENT + 4

pygame.init()
SURFACE = pygame.display.set_mode((900, 500))
FPSCLOCK = pygame.time.Clock()
pygame.display.set_caption("Infernal World")

YELLOW_DRAGON_IMAGE = pygame.image.load("C:\\Users\\annas\\src\\yellow_dragon.png")
YELLOW_DRAGON = pygame.transform.scale(YELLOW_DRAGON_IMAGE,(50, 60))
YELLOW_DRAGON = pygame.transform.flip(YELLOW_DRAGON, True, False)

GREEN_DRAGON_IMAGE = pygame.image.load("C:\\Users\\annas\\src\\green_dragon.png")
GREEN_DRAGON = pygame.transform.scale(GREEN_DRAGON_IMAGE,(50, 60))

BACKGROUND = pygame.transform.scale(pygame.image.load("C:\\Users\\annas\\src\\hell_background.jpg"),(900, 500))

def draw_window(yellow, green, yellow_bullets_small, green_bullets_small,\
                yellow_bullets_big, green_bullets_big, yellow_health, green_health):
    SURFACE.blit(BACKGROUND,(0, 0))
    SURFACE.blit(YELLOW_DRAGON, (yellow.x, yellow.y,))
    SURFACE.blit(GREEN_DRAGON, (green.x, green.y))
    pygame.draw.rect(SURFACE, BLACK, BORDER)

    yellow_health_text = HEALTH_FONT.render("Health:" + str(yellow_health), True, WHITE)
    green_health_text = HEALTH_FONT.render("Health:" + str(green_health), True, WHITE)
    SURFACE.blit(yellow_health_text,(10,10))
    SURFACE.blit(green_health_text,(700, 10))

    #DRAW BULLETS
    for bullet in yellow_bullets_small:
        pygame.draw.rect(SURFACE,CYAN, bullet) 

    for bullet in green_bullets_small:
        pygame.draw.rect(SURFACE,ORCHID, bullet)

    for bullet in yellow_bullets_big:
        pygame.draw.rect(SURFACE,LAWNGREEN, bullet)

    for bullet in green_bullets_big:
        pygame.draw.rect(SURFACE,LAWNGREEN,bullet)

    pygame.display.update()
    
#SMALL BULLETS CLASS
def handle_bullets_small(yellow_bullets_small, green_bullets_small, yellow, green):
    for bullet in yellow_bullets_small:
        bullet.x += BULLET_VEL_SMALL
        if green.colliderect(bullet):
            pygame.event.post(pygame.event.Event(GREEN_HIT_SMALL))
            yellow_bullets_small.remove(bullet)
        elif bullet.x > 900:
            yellow_bullets_small.remove(bullet)

    for bullet in green_bullets_small:
        bullet.x -= BULLET_VEL_SMALL
        if yellow.colliderect(bullet):
            pygame.event.post(pygame.event.Event(YELLOW_HIT_SMALL))
            green_bullets_small.remove(bullet)
        elif bullet.x < 0:
            green_bullets_small.remove(bullet)

#BIG BULLETS CLASS
def handle_bullets_big(yellow_bullets_big, green_bullets_big, yellow, green):
    for bullet in yellow_bullets_big:
        bullet.x += BULLET_VEL_BIG
        if green.colliderect(bullet):
            pygame.event.post(pygame.event.Event(GREEN_HIT_BIG))
            yellow_bullets_big.remove(bullet)
        elif bullet.x > 900:
            yellow_bullets_big.remove(bullet)

    for bullet in green_bullets_big:
        bullet.x -= BULLET_VEL_BIG
        if yellow.colliderect(bullet):
            pygame.event.post(pygame.event.Event(YELLOW_HIT_BIG))
            green_bullets_big.remove(bullet)
        elif bullet.x < 0:
            green_bullets_big.remove(bullet)

def draw_winner(text):
    draw_text = WINNER_FONT.render(text, True, WHITE)
    SURFACE.blit(draw_text, (900/2 - draw_text.get_width()/2,\
                             500/2 - draw_text.get_height()/2))
    pygame.display.update()
    pygame.time.delay(5000)


def main():
    yellow = pygame.Rect(100, 170, 50, 60)
    green = pygame.Rect(750, 170, 50, 60)

    green_health = 15
    yellow_health = 15
    
    running = True
    while running:
        for event in pygame.event.get():
            if event.type == QUIT:
                running = False
                pygame.quit()
                sys.exit()
            
            #create event for bullets(yellow dragon)
            if event.type == KEYDOWN:
                if event.key == K_LCTRL and len(yellow_bullets_small) < MAX_BULLETS_SMALL:
                    bullet = pygame.Rect(yellow.x + yellow.width, yellow.y + yellow.height//2 - 10, 10, 5)
                    yellow_bullets_small.append(bullet)

                if event.key == K_LSHIFT and len(yellow_bullets_big) < MAX_BULLETS_BIG:
                     bullet = pygame.Rect(yellow.x + yellow.width, yellow.y + yellow.height//2 - 10, 8, 8)
                     yellow_bullets_big.append(bullet)

             #create event for bullets(green dragon)
                if event.key == K_RCTRL and len(green_bullets_small) < MAX_BULLETS_SMALL:
                    bullet = pygame.Rect(green.x, green.y + green.height//2 - 10, 10, 5)
                    green_bullets_small.append(bullet)

                
                if event.key == K_RALT and len(green_bullets_big) < MAX_BULLETS_BIG:
                    bullet = pygame.Rect(green.x, green.y + green.height//2 - 10, 8, 8)
                    green_bullets_big.append(bullet)

            # create health reduction event
            if event.type == YELLOW_HIT_SMALL:
                yellow_health -= 1

            if event.type == YELLOW_HIT_BIG:
                yellow_health -= 2

            if event.type == GREEN_HIT_SMALL:
                green_health -= 1

            if event.type == GREEN_HIT_BIG:
                green_health -= 2

        # create winner text

        winner_text = ""

        if yellow_health <= 0:
            winner_text = "GREEN WINS"

        if green_health <= 0:
            winner_text = "YELLOW WINS"

        if winner_text != "":
            draw_winner(winner_text)
            break
            

        FPSCLOCK.tick(60)


        keys_pressed = pygame.key.get_pressed()
        
        #movement for yellow dragon
        if keys_pressed[K_a] and yellow.x > 0: #move left
            yellow.x -= VEL
        if keys_pressed[K_d] and yellow.x + yellow.width < BORDER.x: #move right
            yellow.x += VEL
        if keys_pressed[K_w] and yellow.y > 0: #move up
            yellow.y -= VEL
        if keys_pressed[K_s] and yellow.y + yellow.height < 500:#move down
            yellow.y += VEL

        #movement for green dragon
        if keys_pressed[K_KP4] and green.x > BORDER.x: #move left
            green.x -= VEL
        if keys_pressed[K_KP6] and green.x + green.width< 900: #move right
            green.x += VEL
        if keys_pressed[K_KP8] and green.y > 0: #move up
            green.y -= VEL
        if keys_pressed[K_KP5] and green.y + green.height < 500: #move down
            green.y += VEL

        handle_bullets_small(yellow_bullets_small, green_bullets_small, yellow, green)
        handle_bullets_big(yellow_bullets_big, green_bullets_big, yellow, green)
        draw_window(yellow, green, yellow_bullets_small, green_bullets_small,yellow_bullets_big, green_bullets_big,\
                    yellow_health, green_health)
    main()

if __name__ == '__main__':
    main()
