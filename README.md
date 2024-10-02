# tetris.cpp
## 9/8 更新
```cpp=
#include <bits/stdc++.h>
#include <conio.h>
#include <windows.h>
using namespace std;
#define V vector
V<V<int>> board (24, V<int>(12));
V <int> color = {187, 204, 170, 221, 238, 136, 153};
V<V<int>> Asset[7] = {// 初始方塊
	{{0,0,0,0},{3,3,3,3},{0,0,0,0},{0,0,0,0}}, // |, 187
	{{0,0,0,0},{0,3,3,0},{0,0,3,3},{0,0,0,0}}, // z, 204
	{{0,0,0,0},{0,3,3,0},{3,3,0,0},{0,0,0,0}}, // ㄣ, 170
	{{0,0,0,0},{0,3,3,3},{0,0,3,0},{0,0,0,0}}, // T, 221
	{{0,0,0,0},{0,3,3,0},{0,3,3,0},{0,0,0,0}}, // □, 238
  	{{0,0,0,0},{0,0,0,3},{0,3,3,3},{0,0,0,0}}, // L, 136
	{{0,0,0,0},{0,3,0,0},{0,3,3,3},{0,0,0,0}}  // 色違L, 153
};  
HANDLE hIn,hOut;
//取得後臺處理I/O控制權，hIn=>鍵盤控制  hOut=>螢幕輸出控制
void SetColor(int color = 7) { // 更改顏色

	SetConsoleTextAttribute(hOut, color);
}
void position(int x,int y) {
	static COORD c;
	c.X = x;
	c.Y = y;
	SetConsoleCursorPosition(hOut,c);
} // gotoxy
void spin(V<V<int>>& ast) { // 旋轉
	V<V<int>> ret (4, V<int>(4));
	for (int i = 0; i < 4; i++) {
		for (int j = 0; j < 4; j++) {
			ret[i][j] = ast[3-j][i];
		}
	}
	for (int i = 0; i < 4; i++) {
		for (int j = 0; j < 4; j++) {
			ast[i][j] = ret[i][j];
		}
	}
}
void print () {
	for (int i = 1; i < 24; i++) {
		for (int j = 0; j < 12; j++) {
			if ((i == 23 || j == 0 || j == 11) && i >= 3) {
				cout << "□";
				cout << "  ";
			}
			else {
				SetColor (0);
				cout << "□";
				SetColor ();
				cout << "  ";
			} 
		}
		cout << "\n\n";
	}
}
mt19937 gen;
uniform_int_distribution<int> dis(0, 6);
int random () {
	return dis(gen);
}
double frame = 1000 / 60.0; // fps
int main () {
	hIn = GetStdHandle(STD_INPUT_HANDLE);
	hOut = GetStdHandle(STD_OUTPUT_HANDLE);
	srand(time(nullptr));
	gen = mt19937(rand() ^ rand());
	print ();
	/*SetColor(136); cout<<"□";
	SetColor();
	for (int i = 0; i < 4; i++) {
		for (int j = 0; j < 4; j++) {
			cout << Asset[1][i][j];
		}
		cout << '\n';
	}
	for (int i = 0; i < 4; i++) {
		for (int j = 0; j < 4; j++) {
			cout << Asset[1][3-j][i];
		}
		cout << '\n';
	}*/

}
// □ 旋轉 碰撞 消除 旋轉是否碰撞 版面 hold 美編 記分板 fps 
```
參考資料
* https://javilop.com/gamedev/tetris-tutorial-in-c-platform-independent-focused-in-game-logic-for-beginners/
* https://home.gamer.com.tw/creationDetail.php?sn=4771098
* https://tw511.com/a/01/37676.html
* https://blog.csdn.net/dengjin20104042056/article/details/90552747

## 9/9 更新 
和第一周的東西結合
```cpp=
#include<bits/stdc++.h>
#include<windows.h>
#include<conio.h>
#define V vector
using namespace std;
string window[24];
double frame = 1000.0 / 60;
HANDLE hin, hout;
void gopos(int x, int y) {
	COORD p;
	p.X = x, p.Y = y;
	SetConsoleCursorPosition(hout, p);
}//gotoxy
V <int> color = {187, 68, 170, 85, 102, 204, 17};
V<V<int>> Asset[7] = {// 初始方塊
	{{0, 0, 0, 0}, {3, 3, 3, 3}, {0, 0, 0, 0}, {0, 0, 0, 0}}, // |, 187
	{{0, 0, 0, 0}, {0, 3, 3, 0}, {0, 0, 3, 3}, {0, 0, 0, 0}}, // z, 68
	{{0, 0, 0, 0}, {0, 3, 3, 0}, {3, 3, 0, 0}, {0, 0, 0, 0}}, // ㄣ, 170
	{{0, 0, 0, 0}, {0, 3, 3, 3}, {0, 0, 3, 0}, {0, 0, 0, 0}}, // T, 85
	{{0, 0, 0, 0}, {0, 3, 3, 0}, {0, 3, 3, 0}, {0, 0, 0, 0}}, // □, 102
	{{0, 0, 0, 0}, {0, 0, 0, 3}, {0, 3, 3, 3}, {0, 0, 0, 0}}, // L, 204
	{{0, 0, 0, 0}, {0, 3, 0, 0}, {0, 3, 3, 3}, {0, 0, 0, 0}} // 色違L, 17
};
void SetColor(int color = 7) { // 更改顏色
	SetConsoleTextAttribute(hout, color);
}
void spin(V<V<int>>& ast) { // 旋轉
	V<V<int>> ret (4, V<int>(4));
	for (int i = 0; i < 4; i++) {
		for (int j = 0; j < 4; j++) {
			ret[i][j] = ast[3 - j][i];
		}
	}
	for (int i = 0; i < 4; i++) {
		for (int j = 0; j < 4; j++) {
			ast[i][j] = ret[i][j];
		}
	}
}
void print () {
	for (int i = 1; i < 24; i++) {
		for (int j = 0; j < 12; j++) {
			if ((i == 23 || j == 0 || j == 11) && i >= 3) {
				cout << "□  ";
			}
			else {
				SetColor ();
				cout << "    ";
			}
		}
		cout << "\n\n";
	}
}
void init() {
	hin = GetStdHandle(STD_INPUT_HANDLE);
	hout = GetStdHandle(STD_OUTPUT_HANDLE);
	for (int i = 1; i < 24; i++) {
		for (int j = 0; j < 12; j++) {
			if ((i == 23 || j == 0 || j == 11) && i >= 3) {
				window[i] += "□";
			}
			else window[i] += "  ";
		}
	}
}
mt19937 gen;
uniform_int_distribution<int> rand_piece(0, 6);
int random () { // 亂數產生方塊
	return rand_piece(gen);
}
void ConsoleFullScreen() {
	keybd_event(18, 0, 0, 0);
	keybd_event(13, 0x1c, 0, 0);
	keybd_event(18, 0, 2, 0);
	keybd_event(13, 0, 2, 0);
}
int main() {
	ConsoleFullScreen();
	srand(time(nullptr));
	gen = mt19937(rand());
	init();
	print();
	for (int i = 0; i < 7; i ++) {
		for (int j = 0; j < 4; j++) {
			for (int k = 0; k < 4; k++) {
				if (Asset[i][j][k] == 3) SetColor(color[i]), cout << "□", SetColor();
				else cout << "  ";
				cout << "  ";
			}
			cout << "\n\n";
		}
	}
	return 0;
}//第一周不成器的東西先刪掉了 反正也只有一點點
//□的ascii: -95 -68
```

## 9/15 更新
先把輸出優化
```cpp=
void print () {
	for (int i = 1; i < 24; i++) {
		for (int j = 0; j < 12; j++) {
			if (window[i][j] == '1')
				cout << "□  ";
			else SetColor (), cout << "    ";
		}
		cout << "\n\n";
	}
}
```

加了更多東西 (檢查 清除 碰撞)

```cpp=
#include<bits/stdc++.h>
#include<windows.h>
#include<conio.h>
#define V vector
using namespace std;
int window[24][12];
double frame_t = 1000.0 / 60;
HANDLE hin, hout;
V <int> color = {187, 68, 170, 85, 102, 204, 17};
int lv_frame, level, tetr, cur_x = 4, cur_y = 0, clear_lines = 0, cur_frame;
bool new_pos;
V<V<int>> Asset[7] = {// 初始方塊
	{{0, 0, 0, 0}, {1, 1, 1, 1}, {0, 0, 0, 0}, {0, 0, 0, 0}}, // |, 187
	{{0, 0, 0, 0}, {0, 1, 1, 0}, {0, 0, 1, 1}, {0, 0, 0, 0}}, // z, 68
	{{0, 0, 0, 0}, {0, 1, 1, 0}, {1, 1, 0, 0}, {0, 0, 0, 0}}, // ㄣ, 170
	{{0, 0, 0, 0}, {0, 1, 1, 1}, {0, 0, 1, 0}, {0, 0, 0, 0}}, // T, 85
	{{0, 0, 0, 0}, {0, 1, 1, 0}, {0, 1, 1, 0}, {0, 0, 0, 0}}, // □, 102
	{{0, 0, 0, 0}, {0, 0, 0, 1}, {0, 1, 1, 1}, {0, 0, 0, 0}}, // L, 204
	{{0, 0, 0, 0}, {0, 1, 0, 0}, {0, 1, 1, 1}, {0, 0, 0, 0}} // 色違L, 17
};
void gopos(int x, int y) {
	COORD p;
	p.X = x, p.Y = y;
	SetConsoleCursorPosition(hout, p);
}//gotoxy
void SetColor(int color = 7) { // 更改顏色
	SetConsoleTextAttribute(hout, color);
}
void level_fr() {
	if (level <= 8) lv_frame = 48 - 5 * level;
	else if (level == 9) lv_frame = 6;
	else if (level <= 12) lv_frame = 5;
	else if (level <= 15) lv_frame = 4;
	else if (level <= 18) lv_frame = 3;
	else if (level <= 28) lv_frame = 2;
	else lv_frame = 1;
}
void spin(V<V<int>>& ast) { // 旋轉
	V<V<int>> ret (4, V<int>(4));
	for (int i = 0; i < 4; i++) {
		for (int j = 0; j < 4; j++) {
			ret[i][j] = ast[3 - j][i];
		}
	}
	for (int i = 0; i < 4; i++) {
		for (int j = 0; j < 4; j++) {
			ast[i][j] = ret[i][j];
		}
	}
}
bool check(int tmp_x, int tmp_y) {
	for (int x = tmp_x; x <= 13 && x < tmp_x + 4; x++) {
		for (int y = tmp_y; y <= 25 && y < tmp_y + 4; y++) {
			if ((x > 10 || y > 22 || window[y][x] == 1) && Asset[tetr][y - tmp_y][x - tmp_x]) return false;
		}
	}
	return true;
}
void clear() {
	SetColor();
	for (int x = cur_x; x <= 10 && x < cur_x + 4; x++) {
		for (int y = cur_y; y <= 22 && y < cur_y + 4; y++) {
			if (Asset[tetr][y - cur_y][x - cur_x]) {
				window[y][x] = 0;
				gopos(4 * x, 2 * y);
				cout << "  ";
			}
		}
	}
}
void upd(int k) {
	for (int x = cur_x; x <= 10 && x < cur_x + 4; x++) {
		for (int y = cur_y; y <= 22 && y < cur_y + 4; y++) {
			if (Asset[tetr][y - cur_y][x - cur_x]) {
				window[y][x] = k;
				gopos(4 * x, 2 * y);
				SetColor(color[tetr]), cout << "□", SetColor();
			}
		}
	}
}
void print () {
	for (int i = 0; i < 24; i++) {
		for (int j = 0; j < 12; j++) {
			if ((i == 23 || j == 0 || j == 11) && i >= 3) {
				cout << "□  ";
			}
			else {
				SetColor ();
				cout << "    ";
			}
		}
		cout << "\n\n";
	}
}
void init() {
	hin = GetStdHandle(STD_INPUT_HANDLE);
	hout = GetStdHandle(STD_OUTPUT_HANDLE);
	for (int i = 0; i < 24; i++) {
		for (int j = 0; j < 12; j++) {
			if ((i == 23 || j == 0 || j == 11) && i >= 3) {
				window[i][j] = 1;
			}
			else window[i][j] = 0;
		}
	}
}
mt19937 gen;
uniform_int_distribution<int> rand_piece(0, 6);
int random () {
	return rand_piece(gen);
}
void ConsoleFullScreen() {
	keybd_event(18, 0, 0, 0);
	keybd_event(13, 0x1c, 0, 0);
	keybd_event(18, 0, 2, 0);
	keybd_event(13, 0, 2, 0);
}
int main() {
	ConsoleFullScreen();
	srand(time(nullptr));
	gen = mt19937(rand());
	init();
	print();
	bool game_over = false, new_tetr = true;
	level = 25, level_fr(), cur_frame = lv_frame, new_pos = true;
	while (!game_over) {
		if (cur_frame == 0) {
			cur_frame = lv_frame;
			if (!check(cur_x, cur_y + 1)) {
				if (cur_y == 0) {Sleep(2000); break;}
				upd(1);
				cur_x = 4, cur_y = 0, new_tetr = true;
			}
			else clear(), cur_y++, new_pos = true;
		}
		if (new_tetr)
			tetr = rand_piece(gen), new_tetr = false;
		if (new_pos) upd(2), new_pos = false;
		cur_frame--, gopos(40, 1);
		Sleep(frame_t);
	}
	system("cls"); cout << "GAME OVER!!";
	return 0;
}
//假單
```

## 9/22 更新
優化旋轉
```cpp=
#include<bits/stdc++.h>
#include<windows.h>
#include<conio.h>
#define V vector
using namespace std;
int window[25][12];
double frame_t = 1000.0 / 60;
HANDLE hin, hout;
DWORD cnt;
INPUT_RECORD ir;
V <int> color = {187, 68, 170, 85, 102, 204, 17, 7, 255};
int lv_frame, level, tetr, cur_x = 4, cur_y = 0, clear_lines = 0, cur_frame;
bool new_pos;
V<V<int>> Asset[7] = {// 初始方塊
	{{0, 0, 0, 0}, {1, 1, 1, 1}, {0, 0, 0, 0}, {0, 0, 0, 0}}, // |, 187
	{{0, 0, 0, 0}, {0, 1, 1, 0}, {0, 0, 1, 1}, {0, 0, 0, 0}}, // z, 68
	{{0, 0, 0, 0}, {0, 1, 1, 0}, {1, 1, 0, 0}, {0, 0, 0, 0}}, // ㄣ, 170
	{{0, 0, 0, 0}, {0, 1, 1, 1}, {0, 0, 1, 0}, {0, 0, 0, 0}}, // T, 85
	{{0, 0, 0, 0}, {0, 1, 1, 0}, {0, 1, 1, 0}, {0, 0, 0, 0}}, // □, 102
	{{0, 0, 0, 0}, {0, 0, 0, 1}, {0, 1, 1, 1}, {0, 0, 0, 0}}, // L, 204
	{{0, 0, 0, 0}, {0, 1, 0, 0}, {0, 1, 1, 1}, {0, 0, 0, 0}} // 色違L, 17
};
V<V<int>> org_Asset[7];
void gopos(int x, int y) {
	COORD p;
	p.X = x, p.Y = y;
	SetConsoleCursorPosition(hout, p);
}//gotoxy
void SetColor(int color = 7) { // 更改顏色
	SetConsoleTextAttribute(hout, color);
}
void level_fr() {
	if (level <= 8) lv_frame = 48 - 5 * level;
	else if (level == 9) lv_frame = 6;
	else if (level <= 12) lv_frame = 5;
	else if (level <= 15) lv_frame = 4;
	else if (level <= 18) lv_frame = 3;
	else if (level <= 28) lv_frame = 2;
	else lv_frame = 1;
}
void rspin(int tetris, V<V<int>>& ast) { // R轉
	V<V<int>> ret (4, V<int>(4));
	if (tetris == 3) {
		int a, b;
		for (int i = 0; i < 4; i++) {
			for (int j = 0; j < 4; j++) {
				ret[i][j] = ast[i][j];
				if (abs(i - 1) + abs(j - 2) == 1 && ast[i][j] == 0) {
					ret[i][j] = 1;
					a = i, b = j;
				}
			}
		}
		if (a == 0 && b == 2) ret[1][1] = 0;
		else if (a == 1 && b == 1) ret[2][2] = 0;
		else if (a == 2 && b == 2) ret[1][3] = 0;
		else if (a == 1 && b == 3) ret[0][2] = 0;
	}
	else {
		for (int i = 0; i < 4; i++) {
			for (int j = 0; j < 4; j++) {
				ret[i][j] = ast[3 - j][i];
			}
		}
	}
	for (int i = 0; i < 4; i++) {
		for (int j = 0; j < 4; j++) {
			ast[i][j] = ret[i][j];
		}
	}
}
void lspin(int tetris, V<V<int>>& ast) { // L轉
	V<V<int>> ret (4, V<int>(4));
	if (tetris == 3) {
		int a, b;
		for (int i = 0; i < 4; i++) {
			for (int j = 0; j < 4; j++) {
				ret[i][j] = ast[i][j];
				if (abs(i - 1) + abs(j - 2) == 1 && ast[i][j] == 0) {
					ret[i][j] = 1;
					a = i, b = j;
				}
			}
		}
		if (a == 0 && b == 2) ret[1][3] = 0;
		else if (a == 1 && b == 3) ret[2][2] = 0;
		else if (a == 2 && b == 2) ret[1][1] = 0;
		else if (a == 1 && b == 1) ret[0][2] = 0;
	}
	else {
		for (int i = 0; i < 4; i++) {
			for (int j = 0; j < 4; j++) {
				ret[i][j] = ast[j][3 - i];
			}
		}
	}
	for (int i = 0; i < 4; i++) {
		for (int j = 0; j < 4; j++) {
			ast[i][j] = ret[i][j];
		}
	}

}
bool check(int tmp_x, int tmp_y) {
	for (int x = tmp_x; x <= 13 && x < tmp_x + 4; x++) {
		for (int y = tmp_y; y <= 26 && y < tmp_y + 4; y++) {
			if (x >= 1 && x <= 10 && y >= 0 && y <= 23) {
				if (window[y][x] <= 7 && Asset[tetr][y - tmp_y][x - tmp_x]) return false;
			}
			else {
				if (Asset[tetr][y - tmp_y][x - tmp_x]) return false;
			}
			//if ((x > 10 || y > 23 || window[y][x] <= 7) && Asset[tetr][y - tmp_y][x - tmp_x]) return false;
		}
	}
	return true;
}
void clear() {
	SetColor();
	for (int x = cur_x; x <= 10 && x < cur_x + 4; x++) {
		for (int y = cur_y; y <= 23 && y < cur_y + 4; y++) {
			if (Asset[tetr][y - cur_y][x - cur_x]) {
				window[y][x] = 8;
				gopos(4 * x, 2 * y);
				cout << "  ";
			}
		}
	}
}
void upd(int k) {
	for (int x = cur_x; x <= 10 && x < cur_x + 4; x++) {
		for (int y = cur_y; y <= 23 && y < cur_y + 4; y++) {
			if (Asset[tetr][y - cur_y][x - cur_x]) {
				window[y][x] = k;
				gopos(4 * x, 2 * y);
				SetColor(color[tetr]), cout << "□", SetColor();
			}
		}
	}
}
void ast_control(int tetris) {
	ReadConsoleInput(hin, &ir, 1, &cnt);
	auto type = ir.Event.KeyEvent.wVirtualKeyCode;
	if (type == VK_LEFT) {
		if (cur_x > -2 && check(cur_x - 1, cur_y)) clear(), cur_x--, upd(10);
	}
	else if (type == VK_RIGHT) {
		if (cur_x < 13 && check(cur_x + 1, cur_y)) clear(), cur_x++, upd(10);
	}
	else if (type == VK_DOWN) {
		if (cur_y < 26 && check(cur_x, cur_y + 1)) clear(), cur_y++, upd(10);
	}
	else if (type == VK_UP) {
		rspin(tetris, Asset[tetris]);
		if (check(cur_x, cur_y)) lspin(tetris, Asset[tetris]), clear(), rspin(tetris, Asset[tetris]), upd(10);
	}
}
void init() {
	hin = GetStdHandle(STD_INPUT_HANDLE);
	hout = GetStdHandle(STD_OUTPUT_HANDLE);
	for (int i = 0; i < 7; i++) org_Asset[i] = Asset[i];
	for (int i = 0; i < 25; i++) {
		for (int j = 0; j < 12; j++) {
			if ((i == 24 || j == 0 || j == 11) && i >= 4) {
				cout << "□  ";
				window[i][j] = 7;
			}
			else {
				SetColor ();
				cout << "    ";
				window[i][j] = 8;
			}
		}
		cout << "\n\n";
	}
}

void ConsoleFullScreen() {
	keybd_event(18, 0, 0, 0);
	keybd_event(13, 0x1c, 0, 0);
	keybd_event(18, 0, 2, 0);
	keybd_event(13, 0, 2, 0);
}
int main() {
	ConsoleFullScreen();
	srand(time(nullptr));
	mt19937 gen = mt19937(rand());
	uniform_int_distribution<int> rand_piece(0, 6);
	init();
	bool game_over = false, new_tetr = true;
	level = 15, level_fr(), cur_frame = lv_frame;
	while (!game_over) {
		if (cur_frame == 0) {
			cur_frame = lv_frame;
			if (!check(cur_x, cur_y + 1)) {
				if (cur_y == 0 && !check(cur_x, cur_y)) {Sleep(2000); break;}
				upd(tetr);
				cur_x = 4, cur_y = 0, new_tetr = true;
			}
			else clear(), cur_y++, upd(10);
		}
		if (new_tetr)
			Asset[tetr] = org_Asset[tetr], tetr = rand_piece(gen), new_tetr = false;
		if (kbhit()) {
			ReadConsoleInput(hin, &ir, 1, &cnt);
			ast_control(tetr);
		}
		cur_frame--, gopos(40, 1);
		Sleep(frame_t);
	}
	system("cls"); cout << "GAME OVER!!\n";
	/*for (int i = 0; i < 24; i++) {
		for (int j = 0; j < 12; j++) {
			cout << window[i][j] << ' ';
		}
		cout << "\n\n";
	}*/
	return 0;
}
```

## 9/25 更新

修復旋轉卡牆 bug

目前難題：旋轉卡牆時無法靠牆旋轉、按住左右按鍵就不會下降

新增 debug 數據區

```cpp=
#include<bits/stdc++.h>
#include<windows.h>
#include<conio.h>
#define V vector
using namespace std;
int window[25][12];
double frame_t = 1000.0 / 60;
HANDLE hin, hout;
DWORD cnt;
INPUT_RECORD ir;
V <int> color = {187, 68, 170, 85, 102, 204, 17, 7, 255};
int lv_frame, level, tetr, cur_x = 4, cur_y = 0, clear_lines = 0, cur_frame;
bool new_pos;
V<V<int>> Asset[7] = {// 初始方塊
	{{0, 0, 0, 0}, {1, 1, 1, 1}, {0, 0, 0, 0}, {0, 0, 0, 0}}, // |, 187
	{{0, 0, 0, 0}, {0, 1, 1, 0}, {0, 0, 1, 1}, {0, 0, 0, 0}}, // z, 68
	{{0, 0, 0, 0}, {0, 1, 1, 0}, {1, 1, 0, 0}, {0, 0, 0, 0}}, // ㄣ, 170
	{{0, 0, 0, 0}, {0, 1, 1, 1}, {0, 0, 1, 0}, {0, 0, 0, 0}}, // T, 85
	{{0, 0, 0, 0}, {0, 1, 1, 0}, {0, 1, 1, 0}, {0, 0, 0, 0}}, // □, 102
	{{0, 0, 0, 0}, {0, 0, 0, 1}, {0, 1, 1, 1}, {0, 0, 0, 0}}, // L, 204
	{{0, 0, 0, 0}, {0, 1, 0, 0}, {0, 1, 1, 1}, {0, 0, 0, 0}} // 色違L, 17
};
V<V<int>> org_Asset[7];
void gopos(int x, int y) {
	COORD p;
	p.X = x, p.Y = y;
	SetConsoleCursorPosition(hout, p);
}//gotoxy
void SetColor(int color = 7) { // 更改顏色
	SetConsoleTextAttribute(hout, color);
}
void level_fr() {
	if (level <= 8) lv_frame = 48 - 5 * level;
	else if (level == 9) lv_frame = 6;
	else if (level <= 12) lv_frame = 5;
	else if (level <= 15) lv_frame = 4;
	else if (level <= 18) lv_frame = 3;
	else if (level <= 28) lv_frame = 2;
	else lv_frame = 1;
}
void rspin(int tetris, V<V<int>>& ast) { // R轉
	V<V<int>> ret (4, V<int>(4));
	if (tetris == 3) {
		int a, b;
		for (int i = 0; i < 4; i++) {
			for (int j = 0; j < 4; j++) {
				ret[i][j] = ast[i][j];
				if (abs(i - 1) + abs(j - 2) == 1 && ast[i][j] == 0) {
					ret[i][j] = 1;
					a = i, b = j;
				}
			}
		}
		if (a == 0 && b == 2) ret[1][1] = 0;
		else if (a == 1 && b == 1) ret[2][2] = 0;
		else if (a == 2 && b == 2) ret[1][3] = 0;
		else if (a == 1 && b == 3) ret[0][2] = 0;
	}
	else {
		for (int i = 0; i < 4; i++) {
			for (int j = 0; j < 4; j++) {
				ret[i][j] = ast[3 - j][i];
			}
		}
	}
	for (int i = 0; i < 4; i++) {
		for (int j = 0; j < 4; j++) {
			ast[i][j] = ret[i][j];
		}
	}
}
void lspin(int tetris, V<V<int>>& ast) { // L轉
	V<V<int>> ret (4, V<int>(4));
	if (tetris == 3) {
		int a, b;
		for (int i = 0; i < 4; i++) {
			for (int j = 0; j < 4; j++) {
				ret[i][j] = ast[i][j];
				if (abs(i - 1) + abs(j - 2) == 1 && ast[i][j] == 0) {
					ret[i][j] = 1;
					a = i, b = j;
				}
			}
		}
		if (a == 0 && b == 2) ret[1][3] = 0;
		else if (a == 1 && b == 3) ret[2][2] = 0;
		else if (a == 2 && b == 2) ret[1][1] = 0;
		else if (a == 1 && b == 1) ret[0][2] = 0;
	}
	else {
		for (int i = 0; i < 4; i++) {
			for (int j = 0; j < 4; j++) {
				ret[i][j] = ast[j][3 - i];
			}
		}
	}
	for (int i = 0; i < 4; i++) {
		for (int j = 0; j < 4; j++) {
			ast[i][j] = ret[i][j];
		}
	}

}
bool check(int tmp_x, int tmp_y) {
	for (int x = tmp_x; x <= 13 && x < tmp_x + 4; x++) {
		for (int y = tmp_y; y <= 26 && y < tmp_y + 4; y++) {
			if (x >= 1 && x <= 10 && y >= 0 && y <= 23) {
				if (window[y][x] <= 7 && Asset[tetr][y - tmp_y][x - tmp_x]) return false;
			}
			else {
				if (Asset[tetr][y - tmp_y][x - tmp_x]) return false;
			}
			//if ((x > 10 || y > 23 || window[y][x] <= 7) && Asset[tetr][y - tmp_y][x - tmp_x]) return false;
		}
	}
	return true;
}
void clear() {
	SetColor();
	for (int x = cur_x; x <= 10 && x < cur_x + 4; x++) {
		for (int y = cur_y; y <= 23 && y < cur_y + 4; y++) {
			if (x >= 1 && x <= 10 && y >= 0 && y <= 23 && window[y][x] == 10) {
				window[y][x] = 8;
				gopos(4 * x, 2 * y);
				cout << "  ";
			}
		}
	}
}
void upd(int k) {
	for (int x = cur_x; x <= 10 && x < cur_x + 4; x++) {
		for (int y = cur_y; y <= 23 && y < cur_y + 4; y++) {
			if (Asset[tetr][y - cur_y][x - cur_x]) {
				window[y][x] = k;
				gopos(4 * x, 2 * y);
				SetColor(color[tetr]), cout << "□", SetColor();
			}
		}
	}
}
void ast_control(int tetris) {
	ReadConsoleInput(hin, &ir, 1, &cnt);
	auto type = ir.Event.KeyEvent.wVirtualKeyCode;
	if (type == VK_LEFT) {
		if (cur_x > -2 && check(cur_x - 1, cur_y)) clear(), cur_x--, upd(10);
	}
	else if (type == VK_RIGHT) {
		if (check(cur_x + 1, cur_y)) clear(), cur_x++, upd(10);
	}
	else if (type == VK_DOWN) {
		if (check(cur_x, cur_y + 1)) clear(), cur_y++, upd(10);
	}
	else if (type == VK_UP) {
		rspin(tetris, Asset[tetris]);
		if (check(cur_x, cur_y)) lspin(tetris, Asset[tetris]), clear(), rspin(tetris, Asset[tetris]), upd(10);
		else lspin(tetris, Asset[tetris]);
	}
}
void init() {
	hin = GetStdHandle(STD_INPUT_HANDLE);
	hout = GetStdHandle(STD_OUTPUT_HANDLE);
	for (int i = 0; i < 7; i++) org_Asset[i] = Asset[i];
	for (int i = 0; i < 25; i++) {
		for (int j = 0; j < 12; j++) {
			if ((i == 24 || j == 0 || j == 11) && i >= 4) {
				cout << "□  ";
				window[i][j] = 7;
			}
			else {
				SetColor ();
				cout << "    ";
				window[i][j] = 8;
			}
		}
		cout << "\n\n";
	}
}

void ConsoleFullScreen() {
	keybd_event(18, 0, 0, 0);
	keybd_event(13, 0x1c, 0, 0);
	keybd_event(18, 0, 2, 0);
	keybd_event(13, 0, 2, 0);
}
int main() {
	ConsoleFullScreen();
	srand(time(nullptr));
	mt19937 gen = mt19937(rand());
	uniform_int_distribution<int> rand_piece(0, 6);
	init();
	bool game_over = false, new_tetr = true;
	level = 5, level_fr(), cur_frame = lv_frame;
	while (!game_over) {
		if (cur_frame == 0) {
			cur_frame = lv_frame;
			if (!check(cur_x, cur_y + 1)) {
				if (cur_y == 0 && !check(cur_x, cur_y)) {Sleep(2000); break;}
				upd(tetr);
				cur_x = 4, cur_y = 0, new_tetr = true;
			}
			else clear(), cur_y++, upd(10);
		}
		if (new_tetr)
			Asset[tetr] = org_Asset[tetr], tetr = rand_piece(gen), new_tetr = false;
		if (kbhit()) {
			ReadConsoleInput(hin, &ir, 1, &cnt);
			ast_control(tetr);
		}
		cur_frame--;//, gopos(40, 1);
		for (int i = 0; i < 12; i++) {
			for (int j = 0; j < 25; j++) {
				gopos(50 + i * 4, 2 * j);
				cout << window[j][i] << ' ';
			}
			cout << "\n\n";
		}
		Sleep(frame_t);
	}
	system("cls"); cout << "GAME OVER!!\n";
	/**/
	return 0;
}
```

## 10/27 更新

- 新增左旋、`hard drop`、消行
- 修正旋轉不能貼牆轉
- 按住方向鍵依然可以使方塊原地不動
- 無法使用 `shift、space` 按鍵等功能鍵操作方塊
- 只能在電腦是英文輸入法時才能遊玩

```cpp=
#include<bits/stdc++.h>
#include<windows.h>
#include<conio.h>
#define V vector
using namespace std;
int window[25][12];
double frame_t = 1000.0 / 60;
HANDLE hin, hout;
DWORD cnt;
INPUT_RECORD ir;
V <int> color = {187, 68, 170, 85, 102, 204, 17, 7, 7};
int lv_frame, level, tetr, cur_x = 4, cur_y = 0, clear_lines = 0, cur_frame;
bool new_pos;
V<V<int>> Asset[7] = {// 初始方塊
	{{0, 0, 0, 0}, {1, 1, 1, 1}, {0, 0, 0, 0}, {0, 0, 0, 0}}, // |, 187
	{{0, 0, 0, 0}, {0, 1, 1, 0}, {0, 0, 1, 1}, {0, 0, 0, 0}}, // z, 68
	{{0, 0, 0, 0}, {0, 1, 1, 0}, {1, 1, 0, 0}, {0, 0, 0, 0}}, // ㄣ, 170
	{{0, 0, 0, 0}, {0, 1, 1, 1}, {0, 0, 1, 0}, {0, 0, 0, 0}}, // T, 85
	{{0, 0, 0, 0}, {0, 1, 1, 0}, {0, 1, 1, 0}, {0, 0, 0, 0}}, // □, 102
	{{0, 0, 0, 0}, {0, 0, 0, 1}, {0, 1, 1, 1}, {0, 0, 0, 0}}, // L, 204
	{{0, 0, 0, 0}, {0, 1, 0, 0}, {0, 1, 1, 1}, {0, 0, 0, 0}} // 色違L, 17
};
V<V<int>> org_Asset[7];
void gopos(int x, int y) {
	COORD p;
	p.X = x, p.Y = y;
	SetConsoleCursorPosition(hout, p);
}//gotoxy
void SetColor(int color = 7) { // 更改顏色
	SetConsoleTextAttribute(hout, color);
}
void level_fr() {
	if (level <= 8) lv_frame = 48 - 5 * level;
	else if (level == 9) lv_frame = 6;
	else if (level <= 12) lv_frame = 5;
	else if (level <= 15) lv_frame = 4;
	else if (level <= 18) lv_frame = 3;
	else if (level <= 28) lv_frame = 2;
	else lv_frame = 1;
}
void rspin(int tetris, V<V<int>>& ast) { // R轉
	V<V<int>> ret (4, V<int>(4));
	if (tetris == 3) {
		int a, b;
		for (int i = 0; i < 4; i++) {
			for (int j = 0; j < 4; j++) {
				ret[i][j] = ast[i][j];
				if (abs(i - 1) + abs(j - 2) == 1 && ast[i][j] == 0) {
					ret[i][j] = 1;
					a = i, b = j;
				}
			}
		}
		if (a == 0 && b == 2) ret[1][1] = 0;
		else if (a == 1 && b == 1) ret[2][2] = 0;
		else if (a == 2 && b == 2) ret[1][3] = 0;
		else if (a == 1 && b == 3) ret[0][2] = 0;
	}
	else {
		for (int i = 0; i < 4; i++) {
			for (int j = 0; j < 4; j++) {
				ret[i][j] = ast[3 - j][i];
			}
		}
	}
	for (int i = 0; i < 4; i++) {
		for (int j = 0; j < 4; j++) {
			ast[i][j] = ret[i][j];
		}
	}
}
void lspin(int tetris, V<V<int>>& ast) { // L轉
	V<V<int>> ret (4, V<int>(4));
	if (tetris == 3) {
		int a, b;
		for (int i = 0; i < 4; i++) {
			for (int j = 0; j < 4; j++) {
				ret[i][j] = ast[i][j];
				if (abs(i - 1) + abs(j - 2) == 1 && ast[i][j] == 0) {
					ret[i][j] = 1;
					a = i, b = j;
				}
			}
		}
		if (a == 0 && b == 2) ret[1][3] = 0;
		else if (a == 1 && b == 3) ret[2][2] = 0;
		else if (a == 2 && b == 2) ret[1][1] = 0;
		else if (a == 1 && b == 1) ret[0][2] = 0;
	}
	else {
		for (int i = 0; i < 4; i++) {
			for (int j = 0; j < 4; j++) {
				ret[i][j] = ast[j][3 - i];
			}
		}
	}
	for (int i = 0; i < 4; i++) {
		for (int j = 0; j < 4; j++) {
			ast[i][j] = ret[i][j];
		}
	}

}
bool check(int tmp_x, int tmp_y) {
	for (int x = tmp_x; x <= 13 && x < tmp_x + 4; x++) {
		for (int y = tmp_y; y <= 26 && y < tmp_y + 4; y++) {
			if (x >= 1 && x <= 10 && y >= 0 && y <= 23) {
				if (window[y][x] <= 7 && Asset[tetr][y - tmp_y][x - tmp_x]) return false;
			}
			else {
				if (Asset[tetr][y - tmp_y][x - tmp_x]) return false;
			}
			//if ((x > 10 || y > 23 || window[y][x] <= 7) && Asset[tetr][y - tmp_y][x - tmp_x]) return false;
		}
	}
	return true;
}
void show_current_window() {
	for (int y = 4; y <= 23; y++) {
		for (int x = 1; x <= 10; x++) {
			gopos(4 * x, 2 * y);
			SetColor(color[window[y][x]]);
			cout << "  ";
		}
	}
	SetColor();
}
void clear_line() {
	for (int y = cur_y; y < cur_y + 4; y++) {
		bool _flag = true;
		for (int x = 1; x <= 10; x++) {
			if (window[y][x] > 6) {_flag = false; break;}
		}
		if (_flag) {
			for (int i = y; i >= 4; i--) {
				for (int x = 1; x <= 10; x++) {
					if (i > 4) window[i][x] = window[i - 1][x];
					else window[i][x] = 8;
				}
			}
			y--, clear_lines++;
		}
	}
	show_current_window();
}
void clear() {
	SetColor();
	for (int x = cur_x; x <= 10 && x < cur_x + 4; x++) {
		for (int y = cur_y; y <= 23 && y < cur_y + 4; y++) {
			if (x >= 1 && x <= 10 && y >= 0 && y <= 23 && window[y][x] == 10) {
				window[y][x] = 8;
				gopos(4 * x, 2 * y);
				cout << "  ";
			}
		}
	}
}
void upd(int k) {
	for (int x = cur_x; x <= 10 && x < cur_x + 4; x++) {
		for (int y = cur_y; y <= 23 && y < cur_y + 4; y++) {
			if (Asset[tetr][y - cur_y][x - cur_x]) {
				window[y][x] = k;
				gopos(4 * x, 2 * y);
				SetColor(color[tetr]), cout << "□", SetColor();
			}
		}
	}
}
void ast_control(int tetris) {
	ReadConsoleInput(hin, &ir, 1, &cnt);
	auto type = ir.Event.KeyEvent.wVirtualKeyCode;
	if (type == VK_LEFT) {
		if (check(cur_x - 1, cur_y)) clear(), cur_x--, upd(10);
	}
	else if (type == VK_RIGHT) {
		if (check(cur_x + 1, cur_y)) clear(), cur_x++, upd(10);
	}
	else if (type == VK_DOWN) {
		if (check(cur_x, cur_y + 1)) clear(), cur_y++, upd(10);
	}
	else if (type == VK_UP || type == 0x58) {
		rspin(tetris, Asset[tetris]);
		int _left_board = 0, _right_board = 11;
		for (int x = 0; x < 4; x++) {
			for (int y = 0; y < 4; y++) {
				if (Asset[tetris][y][x] && !_left_board)
					_left_board = -x + 1;
				if (Asset[tetris][y][x]) _right_board = 10 - x;
			}
		}
		cur_x = min(_right_board, max(cur_x, _left_board));
		if (check(cur_x, cur_y)) lspin(tetris, Asset[tetris]), clear(), rspin(tetris, Asset[tetris]), upd(10);
		else lspin(tetris, Asset[tetris]);
	}
	else if (type == VK_SPACE) {
		clear();
		for (; cur_y <= 24; cur_y++) {
			if (!check(cur_x, cur_y)) {
				cur_y--; break;
			}
		}
		upd(10);
	}
	else if (type == 0x58) {
		lspin(tetris, Asset[tetris]);
		int _left_board = 0, _right_board = 11;
		for (int x = 0; x < 4; x++) {
			for (int y = 0; y < 4; y++) {
				if (Asset[tetris][y][x] && !_left_board)
					_left_board = -x + 1;
				if (Asset[tetris][y][x]) _right_board = 10 - x;
			}
		}
		cur_x = min(_right_board, max(cur_x, _left_board));
		if (check(cur_x, cur_y)) rspin(tetris, Asset[tetris]), clear(), lspin(tetris, Asset[tetris]), upd(10);
		else rspin(tetris, Asset[tetris]);
	}
}
void init() {
	hin = GetStdHandle(STD_INPUT_HANDLE);
	hout = GetStdHandle(STD_OUTPUT_HANDLE);
	for (int i = 0; i < 7; i++) org_Asset[i] = Asset[i];
	for (int i = 0; i < 25; i++) {
		for (int j = 0; j < 12; j++) {
			if ((i == 24 || j == 0 || j == 11) && i >= 4) {
				cout << "□  ";
				window[i][j] = 7;
			}
			else {
				SetColor ();
				cout << "    ";
				window[i][j] = 8;
			}
		}
		cout << "\n\n";
	}
}

void ConsoleFullScreen() {
	keybd_event(18, 0, 0, 0);
	keybd_event(13, 0x1c, 0, 0);
	keybd_event(18, 0, 2, 0);
	keybd_event(13, 0, 2, 0);
	//keybd_event(VK_SHIFT, 0x1c, 2, 0);
}
int main() {
	ConsoleFullScreen();
	srand(time(nullptr));
	mt19937 gen = mt19937(rand());
	uniform_int_distribution<int> rand_piece(0, 6);
	init();
	bool game_over = false, new_tetr = true;
	level = 5, level_fr(), cur_frame = lv_frame;
	while (!game_over) {
		if (cur_frame == 0) {
			cur_frame = lv_frame;
			if (!check(cur_x, cur_y + 1)) {
				if (cur_y == 0 && !check(cur_x, cur_y)) {Sleep(2000); break;}
				upd(tetr);
				clear_line();
				if (clear_lines >= 10) clear_lines -= 10, level++, level_fr();
				cur_x = 4, cur_y = 0, new_tetr = true;
			}
			else clear(), cur_y++, upd(10);
		}
		if (new_tetr)
			Asset[tetr] = org_Asset[tetr], tetr = rand_piece(gen), new_tetr = false;
		if (kbhit()) {
			ReadConsoleInput(hin, &ir, 1, &cnt);
			ast_control(tetr);
		}
		cur_frame--;//, gopos(40, 1);
		for (int i = 0; i < 12; i++) {
			for (int j = 0; j < 25; j++) {
				gopos(50 + i * 4, 2 * j);
				cout << window[j][i] << ' ';
			}
			cout << "\n\n";
		}
		Sleep(frame_t);
	}
	system("cls"); cout << "GAME OVER!!\n";
	/**/
	return 0;
}
```
