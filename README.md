# Virtuak-Calculeter
Virtual Calculeter Control by fingers detection using Camera Build on Opencv and cvzone
this calculeter work when two fingers distance less then 50 then tip the button and write on the the input section.then we perfomed airthmetic operation finally tip the button of "=" result show and if you want to cleared just press "c" on your keyboard
![Screenshot 2022-04-22 014554](https://user-images.githubusercontent.com/91630653/164550398-a26e7b95-b5a1-43e7-8dd4-753bdf00cb15.png)

# Code 
import cv2
from cvzone.HandTrackingModule import HandDetector



cap = cv2.VideoCapture(0)
cap.set(3, 1280)
cap.set(4, 720)
detector = HandDetector(detectionCon=0.8)


class Button:
    def __init__(self, pos, width, height, value):
        self.pos = pos
        self.width = width
        self.height = height
        self.value = value

    def draw(self, img):
        cv2.rectangle(img, self.pos, (self.pos[0] + self.width, self.pos[1] + self.height), (200, 200, 100), cv2.FILLED)
        cv2.rectangle(img, self.pos, (self.pos[0] + self.width, self.pos[1] + self.height), (20, 20, 10), 4)
        cv2.putText(img, self.value, (self.pos[0]+ 30, self.pos[1] + 70), cv2.FONT_HERSHEY_PLAIN, 4, (0, 0, 0), 4)

    def click(self,x,y):
        if self.pos[0]<x<self.pos[0]+self.width and self.pos[1]<y<self.pos[1]+self.width:
            cv2.rectangle(img, self.pos, (self.pos[0] + self.width, self.pos[1] + self.height), (240, 230, 120),
                          cv2.FILLED)
            cv2.rectangle(img, self.pos, (self.pos[0] + self.width, self.pos[1] + self.height), (20, 20, 10), 4)
            cv2.putText(img, self.value, (self.pos[0] + 30, self.pos[1] + 70), cv2.FONT_HERSHEY_PLAIN, 3, (250, 230, 250), 4)
            return True
        else:
            return False

buttonlistvalue=[["7","8","9","+"],
                 ["4","5","6","-"],
                 ["1","2","3","/"],
                 ["0",".","=","x"]]
buttonlist=[]
for x in range(4):
    for y in range(4):
        xpos=x*100+800
        ypos=y*100+150
        buttonlist.append(Button((xpos,ypos),100,100,buttonlistvalue[y][x]))
myresult= ""

delaycounter=0
while True:
    success, img = cap.read()
    img = cv2.flip(img, 1)
    hands, img = detector.findHands(img, flipType=False)




    cv2.rectangle(img, (800,70), (800 + 400, 70 + 100), (200, 200, 100), cv2.FILLED)
    cv2.rectangle(img, (800,70), (800 + 400, 70 + 100), (140, 200, 0), 4)
    for button in buttonlist:
        button.draw(img)
    cv2.putText(img, myresult, (810, 130), cv2.FONT_HERSHEY_PLAIN, 3, (240, 240, 240), 3)


    if hands:
        lmList = hands[0]['lmList']
        cursor = lmList[8]
        length, info, img = detector.findDistance(lmList[8][0:2], lmList[12][0:2], img)

        x,y=lmList[8][0:2]
        if length < 55:
            for i , button in enumerate(buttonlist):
                if button.click(x, y) and delaycounter==0:
                    valuelist=buttonlistvalue[int(i%4)][int(i/4)]
                    if valuelist=="=":
                        myresult=str(eval(myresult))
                    else:
                        myresult += valuelist

                    delaycounter= 1

    if delaycounter != 0:
        delaycounter += 1
        if delaycounter > 10:
            delaycounter = 0

    cv2.imshow("Virtual Calculeter", img)
    key=cv2.waitKey(1)
    if key==ord("c"):
        myresult=""


cap.release()
cv2.destryAllWindows()

