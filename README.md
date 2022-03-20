# puzzle_deer
#include <bangtal.h>
#include <stdlib.h>
#include <time.h>
#include<stdio.h>

SceneID scene1, scene2;
ObjectID startButton, restartButton, endButton, sentence;
ObjectID cut[3][3];
clock_t start = clock();
clock_t end = clock();




//260, 118
int locate[3][3][2] = { 263,518, 463,518, 663,518, 263,318, 463, 318, 663,318, 263,118, 463,118, 663,118 };
int d[4][2] = { -1, 0, 0, 1, 1, 0, 0, -1 };
int whiteX, whiteY, dir;
int cnt, num = 2;
int tmp;
bool moved;

char pictureimg[3][3][9] = {
      "cut1.jpg",
      "cut2.jpg",
      "cut3.jpg",
      "cut4.jpg",
      "cut5.jpg",
      "cut6.jpg",
      "cut7.jpg",
      "cut8.jpg",
      "cut9.jpg"
};



ObjectID createObject(const char* image, SceneID scene, int x, int y, bool shown) {
    ObjectID object = createObject(image);
    locateObject(object, scene, x, y);
    if (shown) {
        showObject(object);
    }
    return object;
}


ObjectID whiteHere(int a) {
    whiteX = whiteX + d[a][0];
    whiteY = whiteY + d[a][1];
    return 0;
}

ObjectID whiteHereTrue(int a) {
    whiteX = whiteX + d[a][0];
    whiteY = whiteY + d[a][1];
    moved = true;

    return 0;
}

ObjectID cutswap(int a) {
    tmp = cut[whiteX][whiteY];
    cut[whiteX][whiteY] = cut[whiteX + d[a][0]][whiteY + d[a][1]];
    cut[whiteX + d[a][0]][whiteY + d[a][1]] = tmp;
    return 0;
}



void mouseCallback(ObjectID object, int x, int y, MouseAction action) {

    if (object == startButton) {
        enterScene(scene2);

    }
    if (object == restartButton) {
        startGame(scene1);
    }
    if (object == endButton) {
        endGame();
    }

    
    if (object == cut[whiteX + d[0][0]][whiteY + d[0][1]] && action == MouseAction::MOUSE_DRAG_DOWN) {//0
        locateObject(cut[whiteX + d[0][0]][whiteY + d[0][1]], scene2, locate[whiteX][whiteY][0], locate[whiteX][whiteY][1]);
        locateObject(cut[whiteX][whiteY], scene2, locate[whiteX + d[0][0]][whiteY + d[0][1]][0], locate[whiteX + d[0][0]][whiteY + d[0][1]][1]);
        cutswap(0);
        whiteHereTrue(0);
    }
    //locate[whiteX + d[dir][0]][whiteY + d[dir][1]][0], locate[whiteX + d[dir][0]][whiteY + d[dir][1]][1]
    if (object == cut[whiteX + d[1][0]][whiteY + d[1][1]] && action == MouseAction::MOUSE_DRAG_LEFT) {//1
        locateObject(cut[whiteX + d[1][0]][whiteY + d[1][1]], scene2, locate[whiteX][whiteY][0], locate[whiteX][whiteY][1]);
        locateObject(cut[whiteX][whiteY], scene2, locate[whiteX + d[1][0]][whiteY + d[1][1]][0], locate[whiteX + d[1][0]][whiteY + d[1][1]][1]);
        cutswap(1);
        whiteHereTrue(1);
    }
    if (object == cut[whiteX + d[2][0]][whiteY + d[2][1]] && action == MouseAction::MOUSE_DRAG_UP) {//2
        locateObject(cut[whiteX + d[2][0]][whiteY + d[2][1]], scene2, locate[whiteX][whiteY][0], locate[whiteX][whiteY][1]);
        locateObject(cut[whiteX][whiteY], scene2, locate[whiteX + d[2][0]][whiteY + d[2][1]][0], locate[whiteX + d[2][0]][whiteY + d[2][1]][1]);
        cutswap(2);
        whiteHereTrue(2);
    }
    if (object == cut[whiteX + d[3][0]][whiteY + d[3][1]] && action == MouseAction::MOUSE_DRAG_RIGHT) {//3
        locateObject(cut[whiteX + d[3][0]][whiteY + d[3][1]], scene2, locate[whiteX][whiteY][0], locate[whiteX][whiteY][1]);
        locateObject(cut[whiteX][whiteY], scene2, locate[whiteX + d[3][0]][whiteY + d[3][1]][0], locate[whiteX + d[3][0]][whiteY + d[3][1]][1]);
        cutswap(3);
        whiteHereTrue(3);
    }
    if (moved == true) {

        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                //자리가 맞는지 확인
                if (cut[i][j] == cnt++) {
                    num++;

                }
            }
        }
        if (num == 11) {
            printf("Time:%If\n", (double)(end - start) / CLOCKS_PER_SEC);
            showObject(sentence);
        }
    }
    if (object == sentence) {
        endGame();
    }
}

void change() {
    int t = 10000, tmp;
    int random = 0;
    while (t--) {
        dir = rand() % 4;
        if (0 <= whiteX + d[dir][0] && whiteX + d[dir][0] < 3 && 0 <= whiteY + d[dir][1] && whiteY + d[dir][1] < 3) {

            locateObject(cut[whiteX][whiteY], scene2, locate[whiteX + d[dir][0]][whiteY + d[dir][1]][0], locate[whiteX + d[dir][0]][whiteY + d[dir][1]][1]);
            locateObject(cut[whiteX + d[dir][0]][whiteY + d[dir][1]], scene2, locate[whiteX][whiteY][0], locate[whiteX][whiteY][1]);

            tmp = cut[whiteX][whiteY];
            cut[whiteX][whiteY] = cut[whiteX + d[dir][0]][whiteY + d[dir][1]];
            cut[whiteX + d[dir][0]][whiteY + d[dir][1]] = tmp;


            whiteHere(dir);
        }
    }

}

int main() {
    
    
    srand(time(NULL));
    setMouseCallback(mouseCallback);

    scene1 = createScene("퍼즐", "picture.jpg");
    scene2 = createScene("퍼즐", "backgroundcut1.jpg");

    startButton = createObject("start.png", scene1, 600, 200, true);
    restartButton = createObject("restart.png", scene2, 1000,200, true);
    endButton = createObject("end.png", scene2, 1000, 150, true);
    sentence = createObject("sentence.png", scene2, 600, 440, false);

    whiteX = 0;
    whiteY = 2;
    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {
            cut[i][j] = createObject(pictureimg[i][j]);
            locateObject(cut[i][j], scene2, locate[i][j][0], locate[i][j][1]);
        }
    }


    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {
            showObject(cut[i][j]);
        }

    }

    change();

    startGame(scene1);
    
    
    printf("Time:%If\n", (double)(end - start) / CLOCKS_PER_SEC);

    return 0;


}
