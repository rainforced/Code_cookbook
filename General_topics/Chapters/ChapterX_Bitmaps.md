# Chapter X: Bitmaps
## What is Bitmap
**Bitmap (.bmp)** или **Device Independent Bitmap (DIB) (.dib)** - растровый(те состоящий из отдельных пикселей) формат хранения изображений. Подробно тут про 1) [bmp](https://itnext.io/bits-to-bitmaps-a-simple-walkthrough-of-bmp-image-format-765dc6857393), 2) [raster vs vector images](https://vector-conversions.com/vectorizing/raster_vs_vector.html#:~:text=Vector%20images%20are%20mathematical%20calculations,the%20image%20will%20look%20blurry).      

### Факты о bmp
1. Это lossless format. В нем нет сжатия (как jpeg, png, etc), те каждый пиксель изображения - это отдельный объект (они не групиируются по общим признакам, как в жатых форматах). Можно получит доступ к каждому пикселю изображения. Поэтому, bmp - это самый "тяжелый" формат. 
2. Может иметь разную глубину цвета (**bits-per-pixel** or **bpp**) - те сколько бит выделяется на 1 пиксель. Так, если bpp = 1-bit, то это 0 или 1 (те черный или белый) - монохромное изображение. Bitmap поддреживает 1-bit, 2-bit, 4-bit, 8-bit, 16-bit, 24-bit, and 32-bit глубину цвета.
3. Если bpp = 32-bit, то bitmap может также содержать альфа-канал (те канал прозрачности). 
4. В bitmap формате все записывается по принципу **little-endian** (те это запись числа "слева-направо"), если число более 1-byte. Это значит, что **least-significant byte** (LSB) записывается первым. Например, 312 в hex: 0x00 0x00 0x01 0x38. В little-endian системе 312 будет записываться как 0x38 0x01 0x00 0x00. 

### Составные части файла
Принципиально в файле 2 части: 1) метадата - это информация о файле и изображении, 2) raw pixel data - те просто массив, где записаны RGB составляющие пикселя. При парсинге файла, программа обрабатывает все эти части и строит изображение по массиву пикселей. В файле они идут следующим образом: 
     <img src="https://github.com/PlohoyParen/Cpp_other_doc/blob/master/Documents/image/BMPfileFormat.png" alt = "FTD" width = "700">
1. **File type data** (`BITMAPFILEHEADER`) - информация о файле. Нас больше всего интересует 
     - "File size" - полный размер файла, включающий в себя метадату и PixelArray.
     - "File Offset to PixelArray" - это сколько байтов отступ для raw PixelArray data (те где кончается метадата и начинаются пиксели).
2. **Image information data** или **DIB Header** (`BITMAPINFOHEADER`) - информация по самом изображении: его размеры, bpp, было ли сжатие файла (0 если нет) и тд. 
     - "Image width", "Image height" - задают размеры PixelArray. 
     - "BitsPerPixel" - задает глубину цвета (bpp). 
     - "Colors in Color Tabel" - указывает сколько всего цветов в Color Pallet (см далее). 
     - "Important Color Count" - задает первые n цветов из Color Pallet  как главные (те только для bpp <= 8-bit). Например, пусть у нас есть 50 цветов, но для достаточно качетсвенной передачи изображения нужно только 13 (остальные влияют не так сильно). Тогда мы указываем их первыми, делаем "Important Color Count" = 13. В этому случаи, если у нас не возможности рендерить изображение на 50 цветах (например, слабый дешевый LCD экран), то будут использоваться только превые 13. Важно, что был важные цвета шли впереди внутри Color Pallet, порядок остальных не важен.
3. **Color Pallet** (semi-optional) - это проиндексированная таблица цветов. Если "BitsPerPixel" маелький, то стандартные RGB каналы составить нельзя. Так, 1-bit - это любые 2 цвета; 4-bit - 16 цветов, 8-bit - 256 цветов. Поэтому, создается таблица, которая сопоставляет число-цвет. Это нужно только, если "BitsPerPixel" меньше или равен 8-bit. Если ли же "BitsPerPixel" больше 8-bit, то эта часть файла не нужна тк цвета составляются из смесм RGB каналов. Поэтому Color Pallet is semi-optional.     
     |BitsPerPixel(bpp)| Palletized| Max Colors| Description|
     | --- | --- | --- | --- |
     |1 |Yes | 2 | Ideal for monochromatic image of any two colors defined in the pallet.
     |4 |Yes | 16 |Maximum 16 distinct colors can be defined in the pallet.
     |8 | Yes | 256 | Maximum 256 distinct colors can be defined in the pallet.
     |16 | No | 65_536 | Color is derived from RGB value (5-bit color channel / MSB is ignored). Те 32 на канал: Color(31, 31, 31)
     |24 | No | 16M (16_777_215) | Color is derived from RGB value (8-bit color channel). Те 256 на канал: Color(255, 255, 255)
     |32 | No | 4B (4_294_967_296) | Color is derived from RGBA value (8-bit color channel and 8-bit alpha channel).      
     
     - *Тут надо отметить*, что "Colors in Color Tabel" из DIB header - это общее кол-во цветов в таблице. "Colors in Color Tabel" = 0 значит максимус доступных цветов (2^BitsPerPixel). Если bpp > 8-bit, то всегда указывается 0 (те 2^BitsPerPixel) - максимум доступных цветов из каналов RGB (или RGBA для 32-bit).
4. **PixelArray data**
- Массив нужно читать снизу-вверх слева-направо, те в первом элементе PixelArray лежит правый *нижний* угол картинки. 
- Массив читается рядами: полная прорисовка слева-направо. Это назывется **BMP Scanning** - те прорировывать ряд за рядом.
- **Padding**: каждый ряд PixelArray должен быть кратен 4. Те если информация о пикселях в этом ряду меньше, чем 4xN, то ряд дополняется несколькими байтами, чтобы быть кратным 4. Для вычисления какого рамера padding можно использовать следушую формулу:

## Пример файла bmp прочитаного/написанного в hex редакторе
### 24-bit colors bmp (5x5 pixels)
24-bites color - это 16777216 оттенков (2^24). В этом случаии у нас есть 8-bit на канал (2 разряда в hex). Важно что тут все (вообще все: и headers, и PixelArray) записывается задом-наперед (те  **little-endian**), поэтому вместо RGB записть идет BGR. Как составлялся header подробно [тут](https://itnext.io/bits-to-bitmaps-a-simple-walkthrough-of-bmp-image-format-765dc6857393).      

#### Headers 
```
42 4D
00 00 00 00
00 00
00 00
36 00 00 00

28 00 00 00
05 00 00 00
05 00 00 00
01 00
18 00
00 00 00 00
00 00 00 00
00 00 00 00
00 00 00 00
00 00 00 00
00 00 00 00
```
Тут не нужен блок для **Color Pallete** тк цвета собираются из каналов RGB. Первые 2-bytes `42 4D` - это BM в little-endian (это magic bytes - зарезервированные байты для bmp).

#### PixelArrya    
```
FF FF FF   00 00 00   00 00 00   00 00 00   00 FF FF   00
00 00 00   00 00 00   00 00 00   00 00 00   00 00 00   00
00 00 00   00 00 00   00 FF 00   00 00 00   00 00 00   00
00 00 00   00 00 00   00 00 00   00 00 00   00 00 00   00
00 00 FF   00 00 00   00 00 00   00 00 00   FF 00 00   00
```
Один блок, например `FF FF FF`, - это один пиксель (белый) - BGR. 1-bit в конце `00` - это буфер до кратности 4 (5 pixels X 3 byte (per pixel) = 15-bytes + 1-byte). 

#### Как выглядит на самом деле
На самом деле в файле все числа идут подряд в бинарном виде. Если открыть реальный файл в hex редакторе, то будет что-то подобное:
```
42 4D A2 11 03 00 00 00 00 00 8A 00 00 00 7C 00 00 00 03 01 00 00 C2 00 00 00 01 00 20 00 03 00 00 00 18 11 03 00 27 00 00 00 27 00 00 00 00 00 00 00 00 00 00 00 00 00 FF 00 00 FF 00 00 FF 00 00 00 00 00 00 FF 42 47 52 73 80 C2 F5 28 60 B8 1E 15 20 85 EB 01 40 33 33 13 80 66 66 26 40 66 66 06 A0 99 99 09 3C 0A D7 03 24 5C 8F 32 00 00 00 00 00 00 00 00 00 00 00 00 04 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 60 87 A7 FF 5D 85 A2 FF 6B 8F AD FF 59 7E 98 FF 76 99 B3 FF 8F B4 CA FF 32 52 69 FF 73 94 A8 FF 83 A4 B7 FF 79 9D AF FF 8C AF C3 FF 82 A7 BB FF A2 C9 DF FF 9A C3 D9 FF A3 CB E4 FF B2 DE F6 FF 90 BA D7 FF 9A C7 E2 FF 92 BA D6 FF A6 CF E6 FF AF D5 ED FF A7 CC E0 FF B5 D6 EA FF A3 C4 D7 FF AB C9 DA FF AC CA DB FF AB C9 DC FF 93 B1 C4 FF 6E 8D A2 FF 5F 7E 93 FF 58 78 8F FF 4B 6A 81 FF 48 65 7A FF 48 63 78 FF 43 5E 73 FF 3E 59 6E FF 38 53 68 FF 32 4D 62 FF 2D 48 5D FF 2B 46 5B FF 27 42 57 FF 42 5D 72 FF 45 60 75 FF 56 71 86 FF 4F 6A 7F FF 53 6E 83 FF 52 6D 82 FF 49 64 79 FF 4C 65 79 FF 4B 64 78 FF 5A 73 87 FF 4E 67 7B FF 68 83 98 FF 6B 86 9B FF 6F 8A 9F FF 7C 97 AC FF 76 92 AA FF 8B A7 BF FF 8F AA C4 FF 94 AF C9 FF 90 AE C7 FF 8C AA ...(и так далее)
```
На самом деле легко заметить, где начинается PixelArrya, тк последние header 4-bytes: `0x00 0x00 0x00 0x00` (это ImportantColors	для  BITMAPINFOHEADER).

###  16-bit colors bmp 
16-bit colors - это 32768 оттенков (2^15, по 5-bit на канал + 1 пустой). В этом случаии у нас есть только 2-byte на пиксель (16-bit/8), таким боразом мы будет задавать цвет по 5-bit на канал + 1 пустой (least-significant byte, тк тут все в little-endian, то он будет идти впереди): 
``` 
0  XXXXX XXXXX XXXXX 
    R     G     B
 ```
 А в hex записи придется конвертировать bin в hex. Например, чистый красный: `0111110000000000` => `01111100 00000000` => `0x7C 0x00`. Теперь, применяя little-endian: `0x00 0x7C`. Еще пример, желтый (это красный + зеленый каналы): `0111111111100000` => `01111111 11100000` => `0x7F 0xE0`, применяя little-endian: `0x00 0x7F`.

## Пример прасера bmp на С++ (Chili)
```cpp
Surface::Surface(const std::string& file_name)
{
	std::ifstream in_file(file_name, std::ios::binary);
	
	assert(in_file);
	
	BITMAPFILEHEADER btmFileHeader;
	in_file.read(reinterpret_cast<char*>(&btmFileHeader), sizeof(btmFileHeader));
	
	BITMAPINFOHEADER btmInfoHeader;
	in_file.read(reinterpret_cast<char*>(&btmInfoHeader), sizeof(btmInfoHeader));
	
	assert(btmInfoHeader.biCompression == BI_RGB);
	assert(btmInfoHeader.biBitCount == 24 || btmInfoHeader.biBitCount == 32);

	width = btmInfoHeader.biWidth;
	height = btmInfoHeader.biHeight;
	pPixels = new Color[width * height];    //Color is dword class 

	in_file.seekg(btmFileHeader.bfOffBits, std::ios_base::beg);

	if (btmInfoHeader.biBitCount == 24)
	{
		const int padding = (4 - (width * 3) % 4) % 4;
		for (int y = height - 1; y >= 0; y--)	     //column
		{
			for (int x = 0; x < width; x++)		//row
			{
				int blue = in_file.get();
				int green = in_file.get();
				int red = in_file.get();
				AddPixel(x, y, Color(red, green, blue));
			}
			in_file.seekg(padding, std::ios_base::cur);
		}
	}
	else if(btmInfoHeader.biBitCount == 32)
	{
		const int padding = (4 - (width * 4) % 4) % 4;
		for (int y = height - 1; y >= 0; y--)	     //column
		{
			for (int x = 0; x < width; x++)		//row
			{
				int blue = in_file.get();
				int green = in_file.get();
				int red = in_file.get();
				AddPixel(x, y, Color(red, green, blue));
			}
			in_file.seekg(padding, std::ios_base::cur);
		}
	}
}
```
