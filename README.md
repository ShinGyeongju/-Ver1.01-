









// 영상처리 프로그램 ver1.01













#define _CRT_SECURE_NO_WARNINGS

#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <math.h>
#include <Windows.h>	// 픽셀을 출력해주는 기능 포함

///////////////////////////////
//	함수 선언부
///////////////////////////////
void print_Menu();
void open_Image();
unsigned char** malloc2D(int, int);
void display_Image();
void free_Input_Image();
void free_Output_Image();
void save_Image();
void equal_Image();
void bw_Image();
void bright_Image();
void reverse_Image();
void scaleUpDown_Image();
void move_Image();
void rotate_Image();
void mosaic_Image();
void edge_Image();

///////////////////////////////
//	전역 변수 선언부
///////////////////////////////
unsigned char** inImage = NULL, ** outImage = NULL;
int inH = 0, inW = 0, outH = 0, outW = 0;
char fileName[200];
HWND hwnd;				// 윈도우 화면용
HDC hdc;				// 윈도우 화면용

///////////////////////////////
//	메인 함수
///////////////////////////////
void main() {
	char select = ' ';

	hwnd = GetForegroundWindow();
	hdc = GetWindowDC(hwnd);

	while (select != '2') {
		print_Menu();
		select = getche();

		switch (select) {
		case '0':
			open_Image();
			printf("\n# 계속하려면 아무키나 눌러주세요. #");
			getche();
			break;
		case '1':
			save_Image();
			printf("\n# 계속하려면 아무키나 눌러주세요. #");
			getche();
			break;
		case '2':
			break;
		case 'a':
		case 'A':
			equal_Image();
			printf("\n# 계속하려면 아무키나 눌러주세요. #");
			getche();
			break;
		case 'b':
		case 'B':
			bright_Image();
			printf("\n# 계속하려면 아무키나 눌러주세요. #");
			getche();
			break;
		case 'c':
		case 'C':
			reverse_Image();
			printf("\n# 계속하려면 아무키나 눌러주세요. #");
			getche();
			break;
		case 'd':
		case 'D':
			bw_Image();
			printf("\n# 계속하려면 아무키나 눌러주세요. #");
			getche();
			break;
		case 'e':
		case 'E':
			scaleUpDown_Image();
			printf("\n# 계속하려면 아무키나 눌러주세요. #");
			getche();
			break;
		case 'f':
		case 'F':
			move_Image();
			printf("\n# 계속하려면 아무키나 눌러주세요. #");
			getche();
			break;
		case 'g':
		case 'G':
			rotate_Image();
			printf("\n# 계속하려면 아무키나 눌러주세요. #");
			getche();
			break;
		case 'h':
		case 'H':
			mosaic_Image();
			printf("\n# 계속하려면 아무키나 눌러주세요. #");
			getche();
			break;
		case 'i':
		case 'I':
			edge_Image();
			printf("\n# 계속하려면 아무키나 눌러주세요. #");
			getche();
			break;
		default:
			printf("\n\n# 잘못된 문자입니다. 계속하려면 아무키나 눌러주세요. #");
			getche();
			break;
		}
	}

	free_Input_Image();
	free_Output_Image();
}

///////////////////////////////
//	공통 함수 정의부
///////////////////////////////
void print_Menu() {		// 메뉴 출력
	system("cls");
	system("mode con cols=85 lines=30");

	puts("\n @========================= 영상처리 프로그램 (Ver1.01) =========================@\n");
	puts("\t 0 : 열기\t1 : 저장\t2 : 종료\n");
	puts("\t A : 원본 출력\tB : 밝기 조절\tC : 반전 출력\tD : 흑백 출력");
	puts("\t E : 크기 조절\tF : 이동 출력\tG : 회전 출력\tH : 모자이크 출력");
	puts("\t I : 경계선 출력\n");
	printf(" 문자를 입력하세요. : ");
}
void open_Image() {		// 이미지 불러오기
	strcpy(fileName, "C:\\images\\RAW\\");
	char tmpName[100];

	printf("\n # 파일명을 입력하세요. : ");
	scanf("%s", tmpName);
	strcat(fileName, tmpName);
	strcat(fileName, ".raw");

	FILE* rfp = fopen(fileName, "rb");
	if (rfp == NULL) {
		MessageBox(hwnd, L"존재하지 않는 파일입니다.", L"출력창", NULL);
		return;
	}

	fseek(rfp, 0L, SEEK_END);
	long fileSize = ftell(rfp);			// 파일 크기 저장
	fclose(rfp);

	free_Input_Image();
	free_Output_Image();

	rfp = fopen(fileName, "rb");
	inH = inW = (int)sqrt(fileSize);

	inImage = malloc2D(inH, inW);
	for (int i = 0; i < inH; i++) {
		fread(inImage[i], sizeof(unsigned char), inW, rfp);
	}
	fclose(rfp);

	printf("[ %s ]파일을 불러왔습니다.\n", fileName);
}
unsigned char** malloc2D(int h, int w) {	// 동적메모리 할당
	unsigned char** returnPointer = (unsigned char**)malloc(sizeof(unsigned char*) * h);

	for (int i = 0; i < h; i++) {
		returnPointer[i] = (unsigned char*)malloc(sizeof(unsigned char) * w);
	}
	
	return returnPointer;
}
void display_Image() {		// 이미지 출력
	system("cls");

	unsigned char pixel;
	for (int i = 0; i < outH; i++) {
		for (int k = 0; k < outW; k++) {
			pixel = outImage[i][k];
			SetPixel(hdc, k + 100, i + 100, RGB(pixel, pixel, pixel));
		}
	}
}
void free_Input_Image() {		// 동적메모리 해제
	if (inImage == NULL) {
		return;
	}

	for (int i = 0; i < inH; i++) {
		free(inImage[i]);
	}
	free(inImage);

	inImage = NULL;
}
void free_Output_Image() {		// 동적메모리 해제
	if (outImage == NULL) {
		return;
	}

	for (int i = 0; i < outH; i++) {
		free(outImage[i]);
	}
	free(outImage);

	outImage = NULL;
}
void save_Image() {		// 이미지 저장
	if (outImage == NULL) {
		puts("\n # 저장 할 이미지가 없습니다.");
		return;
	}

	char saveFileName[200] = "C:\\images\\RAW\\";
	char tmpFileName[50];

	printf("\n저장할 파일명 : ");
	scanf("%s", tmpFileName);

	strcat(saveFileName, tmpFileName);
	strcat(saveFileName, ".raw");

	FILE* wfp = fopen(saveFileName, "wb");
	for (int i = 0; i < outH; i++) {
		fwrite(outImage[i], sizeof(unsigned char), outW, wfp);
	}
	fclose(wfp);

	printf(" [ %s ]파일로 저장됐습니다.\n", saveFileName);
}

///////////////////////////////
//	영상처리 함수 정의부
///////////////////////////////
void equal_Image() {		// 원본 출력
	if (inImage == NULL) {
		return;
	}
	free_Output_Image();

	outH = inH;
	outW = inW;
	outImage = malloc2D(outH, outW);

	for (int i = 0; i < outH; i++) {
		for (int k = 0; k < outW; k++) {
			outImage[i][k] = inImage[i][k];
		}
	}
	display_Image();
}
void bw_Image() {		// 흑백 출력 (픽셀의 평균값 기준)
	if (inImage == NULL) {
		return;
	}
	free_Output_Image();

	outH = inH;
	outW = inW;
	outImage = malloc2D(outH, outW);

	int sum = 0;
	for (int i = 0; i < outH; i++) {
		for (int k = 0; k < outW; k++) {
			sum += inImage[i][k];
		}
	}
	double avg = (double)sum / (outH * outW);

	for (int i = 0; i < outH; i++) {
		for (int k = 0; k < outW; k++) {
			if (inImage[i][k] < avg) {
				outImage[i][k] = 0;
			}
			else {
				outImage[i][k] = 255;
			}
		}
	}
	display_Image();
}
void bright_Image() {		// 밝기 조절
	if (inImage == NULL) {
		return;
	}
	free_Output_Image();

	outH = inH;
	outW = inW;
	outImage = malloc2D(outH, outW);

	int bright;
	printf("\n # 밝기와 크기를 입력하세요. (+:밝게/-:어둡게) : ");
	scanf("%d", &bright);

	for (int i = 0; i < outH; i++) {
		for (int k = 0; k < outW; k++) {
			if ((inImage[i][k] + bright) > 255) {
				outImage[i][k] = 255;
			}
			else if ((inImage[i][k] + bright) < 0) {
				outImage[i][k] = 0;
			}
			else {
				outImage[i][k] = (inImage[i][k] + bright);
			}
		}
	}
	display_Image();
}
void reverse_Image() {		// 반전 출력
	if (inImage == NULL) {
		return;
	}
	free_Output_Image();

	outH = inH;
	outW = inW;
	outImage = malloc2D(outH, outW);

	printf("\n # 문자를 입력하세요. (A : 좌우 반전\tB : 상하 반전\tC : 명암 반전) : ");
	char select = getche();

	int out = 0;
	for (int i = 0; i < outH; i++) {
		for (int k = 0; k < outW; k++) {
			if (select == 'a' || select == 'A') {
				outImage[i][k] = inImage[i][outW - k - 1];
			}
			else if (select == 'b' || select == 'B') {
				outImage[i][k] = inImage[outH - i - 1][k];
			}
			else if (select == 'c' || select == 'C') {
				outImage[i][k] = (255 - inImage[i][k]);
			}
			else {
				printf("\n\n# 잘못된 문자입니다. 계속하려면 아무키나 눌러주세요. #");
				getche();
				out = 1;
				break;
			}
		}
		if (out) {
			break;
		}
	}
	if (!out) {
		display_Image();
	}
}
void scaleUpDown_Image() {		// 크기 조절
	if (inImage == NULL) {
		return;
	}
	free_Output_Image();

	outH = inH * 2;
	outW = inW * 2;
	outImage = malloc2D(outH, outW);

	printf("\n # 문자를 입력하세요. (A : 2배 확대\tB : 2배 축소) : ");
	char select = getche();

	if (select == 'a' || select == 'A') {
		outH = inH * 2;
		outW = inW * 2;
		outImage = malloc2D(outH, outW);

		for (int i = 0; i < outH; i++) {
			for (int k = 0; k < outW; k++) {
				outImage[i][k] = inImage[i / 2][k / 2];
			}
		}
		system("mode con cols=130 lines=48");
		display_Image();
	}
	else if (select == 'b' || select == 'B') {
		outH = inH / 2;
		outW = inW / 2;
		outImage = malloc2D(outH, outW);

		for (int i = 0; i < outH; i++) {
			for (int k = 0; k < outW; k++) {
				outImage[i][k] = inImage[i * 2][k * 2];
			}
		}
		display_Image();
	}
	else {
		printf("\n\n# 잘못된 문자입니다. 계속하려면 아무키나 눌러주세요. #");
		getche();
		return;
	}
}
void move_Image() {		// 이동 출력
	if (inImage == NULL) {
		return;
	}
	free_Output_Image();

	outH = inH;
	outW = inW;
	outImage = malloc2D(outH, outW);

	int x, y;
	printf("\n # 가로축의 이동 방향과 거리를 입력하세요. (+:우/-:좌) : ");
	scanf("%d", &x);
	printf("\n # 세로축의 이동 방향과 거리를 입력하세요. (+:하/-:상) : ");
	scanf("%d", &y);

	for (int i = 0; i < outH; i++) {
		for (int k = 0; k < outW; k++) {
			outImage[i][k] = 0;
		}
	}

	for (int i = 0; i < outH; i++) {
		if ((i + y) < 0 || (i + y) >= outH) {
			continue;
		}
		for (int k = 0; k < outW; k++) {
			if ((k + x) < 0 || (k + x) >= outW) {
				continue;
			}
			outImage[i + y][k + x] = inImage[i][k];
		}
	}
	display_Image();
}
void rotate_Image() {		// 회전 출력
	if (inImage == NULL) {
		return;
	}
	free_Output_Image();

	double deg;
	printf("\n # 회전할 각도를 입력하세요. (시계방향) : ");
	scanf("%lf", &deg);
	double PI = 3.141592;
	double rad = deg * PI / 180.0;

//	outH = (int)((inH * cos(rad) + inW * cos(PI / 2.0 - rad)));
//	outW = (int)((inH * cos(PI / 2.0 - rad) + inW * cos(rad)));
	outH = inH;
	outW = inW;
	outImage = malloc2D(outH, outW);

	int new_x, new_y;
	int center_x = inW / 2, center_y = inH / 2;

	for (int i = 0; i < outH; i++) {
		for (int k = 0; k < outW; k++) {
			new_x = (int)((i - center_y) * sin(rad) + (k - center_x) * cos(rad) + center_x);
			new_y = (int)((i - center_y) * cos(rad) - (k - center_x) * sin(rad) + center_y);
			if (new_y < 0 || new_y >= outH) {
				outImage[i][k] = 0;
			}
			else if (new_x < 0 || new_x >= outW) {
				outImage[i][k] = 0;
			}
			else {
				outImage[i][k] = inImage[new_y][new_x];
			}
		}
	}
	display_Image();
}
void mosaic_Image() {		// 모자이크 출력
	if (inImage == NULL) {
		return;
	}
	free_Output_Image();

	outH = inH;
	outW = inW;
	outImage = malloc2D(outH, outW);

	int mosaic, sum;

	printf("\n # 문자를 입력하세요. (A : 약하게\tB : 강하게) : ");
	char select = getche();

	if (select == 'a' || select == 'A') {
		mosaic = 8;
	}
	else if (select == 'b' || select == 'B') {
		mosaic = 16;
	}
	else {
		printf("\n\n# 잘못된 문자입니다. 계속하려면 아무키나 눌러주세요. #");
		getche();
		return;
	}

	for (int i = 0; i < outH; i += mosaic) {
		for (int k = 0; k < outW; k += mosaic) {
			sum = 0; 

			for (int a = 0; a < mosaic; a++) {
				for (int b = 0; b < mosaic; b++) {
					sum += inImage[i + a][k + b];
				}
			}
			for (int a = 0; a < mosaic; a++) {
				for (int b = 0; b < mosaic; b++) {
					outImage[i + a][k + b] = sum / mosaic;
				}
			}
		}
	}
	display_Image();
}
void edge_Image() {		// 경계선 출력 (sobel mask 이용)
	if (inImage == NULL) {
		return;
	}
	free_Output_Image();

	outH = inH;
	outW = inW;
	outImage = malloc2D(outH, outW);

	int mask_x[3][3] = { {-1,0,1},{-2,0,2},{-1,0,1} };
	int mask_y[3][3] = { {-1,-2,-1},{0,0,0},{1,2,1} };

	for (int i = 0; i < outH; i++) {
		for (int k = 0; k < outW; k++) {
			outImage[i][k] = 0;
		}
	}
	for (int i = 1; i < outH - 1; i++) {
		for (int k = 1; k < outW - 1; k++) {
			float sum_x = 0, sum_y = 0;

			for (int n = 0; n < 3; n++) {
				for (int m = 0; m < 3; m++) {
					sum_x += mask_x[n][m] * inImage[i + n - 1][k + m - 1];
					sum_y += mask_y[n][m] * inImage[i + n - 1][k + m - 1];
				}
			}
			float mag = sqrtf(sum_x * sum_x + sum_y * sum_y);
			if (mag > 255) {
				mag = 255;
			}
			else if (mag < 0) {
				mag = 0;
			}
			outImage[i][k] = (int)mag;
		}
	}
	display_Image();
}
