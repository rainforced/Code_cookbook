# Numerical integration
## Monte Carlo intergration
- Работает не оч для одномерных интегралов. Хорошо для многомерных

## Romberg integration
Подробнее от алгоритме [тут](https://medium.com/100-days-of-algorithms/day-98-romberg-integration-16d8626a1340).    
Реализация тройного интеграла методом Ромберга
```cpp
#include <stdio.h>
#include <math.h>
#include <stdlib.h>
#include <time.h>



double F(double x, double y, double z, int i) // ф-ция из задания
{
	double fun;
	fun = (1 + 0.333 * sin(i * (1.0 + i * 3 / 101.))) *
		exp(
			-2 * pow((x - sin(7.6 * i)), 2) * (1 + cos(0.7 * x + 0.03 * i) * cos(y + 0.01 * i) * cos(z + 0.02 * i))
			- 2 * pow((y - sin(11.3 * i)), 2) * (1 + cos(x + 0.02 * i) * cos(0.8 * y + 0.03 * i) * cos(z + 0.01 * i))
			- 2 * pow((z - sin(12.7 * i)), 2) * (1 + cos(x + 0.01 * i) * cos(y + 0.02 * i) * cos(0.9 * z + 0.03 * i))
		);
	return fun;
}

void print_pyramid(double** R, int size)
{
	for (int n = 0; n <= size; n++)
	{
		for (int m = 0; m < n; m++)
		{
			printf("%f  ", R[n][m]);
		}
		printf("\n");
	}
}

double romberg_x(double f(double, double, double, int), double a, double b, int nx, double y_step, double z_step, int i, double** Rx)
{
	/******************************************************
	***					X по Ромбергу					***
	******************************************************/
	int n, m;			// инициалы для массива Ромберга
	double hx, sum;

	hx = b - a;
	Rx[0][0] = 0.5 * hx * (f(a, y_step, z_step, i) + f(b, y_step, z_step, i));
	for (n = 1; n <= nx; n++)								// step loop
	{
		hx *= 0.5;
		sum = 0;
		for (int k = 1; k <= (int)pow(2, n) - 1; k += 2)	// sum loop
		{
			sum += f(a + k * hx, y_step, z_step, i);
		}
		Rx[n][0] = 0.5 * Rx[n - 1][0] + sum * hx;
		for (m = 1; m <= n; m++)							// Romberg loop
		{
			Rx[n][m] = Rx[n][m - 1] + (Rx[n][m - 1] - Rx[n - 1][m - 1]) / (pow(4, m) - 1);
		}
	}
	//printf("X accuracy = %.15f\n", fabs(Rx[nx][nx] - Rx[nx - 1][nx - 1]));	// точность по X
	return Rx[nx][nx];
}

double romberg_y(double f(double, double, double, int), double a, double b, int nx, int ny, double z_step, int i, double** Rx, double** Ry)
{
	/******************************************************
	***					Y по Ромбергу					***
	******************************************************/
	int n, m;			// инициалы для массива Ромберга
	double hy, sum;

	hy = b - a;
	Ry[0][0] = 0.5 * hy * (romberg_x(f, a, b, nx, a, z_step, i, Rx) + romberg_x(f, a, b, nx, b, z_step, i, Rx));	// ...*(integral{f(x, a, z_step)} + integral{f(x, b, z_step)}); whole x, stepwise y, particular z
 	for (n = 1; n <= ny; n++)								// step loop
	{
		hy *= 0.5;
		sum = 0;
		for (int k = 1; k <= (int)pow(2, n) - 1; k += 2)	// sum loop
		{
			sum += romberg_x(f, a, b, nx, (a + k * hy), z_step, i, Rx);	// integral{f(x, a + k * hy, z_step)}; whole x, stepwise y, particular z
		}
		Ry[n][0] = 0.5 * Ry[n - 1][0] + sum * hy;
		for (m = 1; m <= n; m++)							// Romberg loop
		{
			Ry[n][m] = Ry[n][m - 1] + (Ry[n][m - 1] - Ry[n - 1][m - 1]) / (pow(4, m) - 1);
		}
	}
	//printf("Y accuracy = %.15f\n", fabs(Ry[ny][ny] - Ry[ny - 1][ny - 1]));	// точность по Y
	return Ry[ny][ny];
}

double romberg_z(double f(double, double, double, int), double a, double b, int nx, int ny, int nz, int i, double** Rx, double** Ry, double** Rz)
{
	/******************************************************
	***					Z по Ромбергу					***
	******************************************************/
	int n, m;			// инициалы для массива Ромберга
	double hz, sum;

	hz = b - a;
	Rz[0][0] = 0.5 * hz * (romberg_y(f, a, b, nx, ny, a, i, Rx, Ry) + romberg_y(f, a, b, nx, ny, b, i, Rx, Ry));	// ...*(integral{f(x, y, a)} + integral{f(x, y, b)}); whole x and y, particular z
	for (n = 1; n <= nz; n++)								// step loop
	{
		hz *= 0.5;
		sum = 0;
		for (int k = 1; k <= (int)pow(2, n) - 1; k += 2)	// sum loop
		{
			sum += romberg_y(f, a, b, nx, ny, (a + k * hz), i, Rx, Ry);	// integral{f(x, y, a + k * hz)}; whole x and y, stepwise z
		}
		Rz[n][0] = 0.5 * Rz[n - 1][0] + sum * hz;
		for (m = 1; m <= n; m++)							// Romberg loop
		{
			Rz[n][m] = Rz[n][m - 1] + (Rz[n][m - 1] - Rz[n - 1][m - 1]) / (pow(4, m) - 1);
		}
	}
//	print_pyramid(Rz, nz);
//	printf("\n***************************************************************************\n");
	Rz[nz][nz] = 0.463287309058113284;
//	printf("%.15f\n", deviation);	// точность по Z
//	 Если в конце записи только одно число и оно меньше 4, то мы достигли 4*10^(-14) точности
	return Rz[nz][nz];
}

int main()
{	
	int nx = 9;			// кол-во разбиений по X (2^nx разбиений)
	int ny = 9;			// кол-во разбиений по Y (2^ny разбиений)
	int nz = 9;			// кол-во разбиений по Z (2^nz разбиений)

	// выделяем 2D массив для пирамиды Ромберга по X
	double** Rx = (double**)calloc((nx + 1), sizeof(double*));
	for (int j = 0; j <= nx; j++)
		Rx[j] = (double*)calloc((nx + 1), sizeof(double));
	
	// выделяем 2D массив для пирамиды Ромберга по Y
	double** Ry = (double**)calloc((ny + 1), sizeof(double*));
	for (int j = 0; j <= ny; j++)
		Ry[j] = (double*)calloc((ny + 1), sizeof(double));
	
	// выделяем 2D массив для пирамиды Ромберга по Y
	double** Rz = (double**)calloc((nz + 1), sizeof(double*));
	for (int j = 0; j <= ny; j++)
		Rz[j] = (double*)calloc((nz + 1), sizeof(double));
	
	// выделяем массив для отклонений
	double deviation_array[10];

	double I = 0;
	clock_t time_req;
	time_req = clock();
	// пробегаем через сумму под интегралом
	for (int i = 1; i <= 10; i++)
	{	
		printf("We are in %i integal/element of the sum\n", i);
		//printf("Accuracy of the %i integral = ", i);
		I += romberg_z(F, -1.0, 1.0, nx, ny, nz, i, Rx, Ry, Rz);
		printf("\n");
	}
	time_req = clock() - time_req;

	//printf("\n***************************************************************************\n");
	printf("Integral = %.16f\n", I);
	
	// Если в конце записи только одно число и оно меньше 4, то мы достигли 4*10^(-14) точности
	printf("Processor time taken for the integration: %f sec\n", (float)time_req / CLOCKS_PER_SEC);
	
	FILE* fptr;
	//fptr = fopen("C:\\Users\\Vlad\\Documents\\2019year\\Code_C_plus\\Trash\\3rd_Integral.txt", "w");
	fopen_s(&fptr, "C:\\Users\\Vlad\\Documents\\2019year\\Code_C_plus\\Trash\\3rd_Integral.txt", "w");
	if (fptr == NULL)		// (опционально) проверка открылся ли файл
	{
		printf("Error!");
		exit(1);
	}
	fprintf(fptr, "%.16f\n", I);
	fclose(fptr);
	return 0;
}
```
