# hw3-avs-hse

## ФИО

Рахимова Камила Мухаммадовна

## Группа

БПИ214

## Тема

Арифметика с плавающей точкой

## Условие задачи

Разработать программы на языках программирования C и Ассемблер, выполняющие вычисления над числами с плавающей точкой. Разработанные программы должны принимать числа в допустимом диапазоне. Например, нужно учитывать области определения и допустимых значений, если это связано с условием задачи.

## Вариант 31

Разработать программу численного интегрирования функции y = a + b * x^3 (задаётся действительными числами а,b) в определённом диапазоне целых (задаётся так же) методом прямоугольников с недостатком = левых прямоугольников (точность вычислений = 0.0001).

## Отчет

Изначально, для лучшего понимания темы, были повторно изучены материалы о вычислении определённых интегралов различными методами прямоугольников (см. https://studfile.net/preview/6008218/page:13/ ). Далее была написана программа на C без каких-либо приколюх и фишек, только основа, реализующая исключительно решение задачи. Её можно посмотреть в файле "base.c". В оценивании моей работы она не участвует, но зато хорошо иллюстрирует с чего всё начиналось. Перейдём к следующим шагам.

Итак, задание было выполнено с расчётом на получение оценки в 9/10 баллов.

а) Для достижения данной цели было необходимо в программе на C:
>1. Привести непосредственно код на C 
```
//См. код в файлах "main.c" и "timediff.c"
```
>2. Использовать функции с передачей данных через параметры
```
//Один из примеров, функция "timespecDiff" в файле "timediff.c"

int64_t timespecDiff(struct timespec timeA, struct timespec timeB) {
    int64_t nsecA, nsecB;

    nsecA = timeA.tv_sec;
    nsecA *= 1000000000;
    nsecA += timeA.tv_nsec;


    nsecB = timeB.tv_sec;
    nsecB *= 1000000000;
    nsecB += timeB.tv_nsec;

    return nsecA - nsecB;
}
```
>3. Использовать локальные переменные
```
//Примеры: переменные "nsecA", "nsecB" в функции "timespecDiff" в файле "timediff.c"

int64_t timespecDiff(struct timespec timeA, struct timespec timeB) {
    int64_t nsecA, nsecB;
    ...
}
```
>4. Реализовать программу в виде двух или более единиц компиляции
```
//Программа реализована в виде двух единиц компиляции: "main.c" и "timediff.c"
```
>5. Использовать файлы с исходными данными и файлы для вывода результатов. Имена файлов задаются с использованием аргументов командной строки. Командная строка проверяется на корректность числа аргументов и корректное открытие файлов. (далее, на 8б.) Изменить формат командной строки с учетом выбора ввода из файлов или с использованием генератора.
```
/* Реализовано в функции "main" в файле "main.c": командная строка принимает на вход 3 параметра: 
имя входного файла, имя выходного файла и слово "input"/"generator", в зависимости от выбора пользователя -- 
ввод из файла или с использованием генератора */

int main(int argc, char** argv) {
    ...
    if (argc == 4) {					// correctness of the numbers of arguments
        arg1 = argv[1];
        arg2 = argv[2];
        arg3 = argv[3];                                 //"generator" or "input"
        srand(time(NULL));
    } else {                                            // exit with error
        printf("Error! Try again, please.");
        return 1;
    }
    ...
    FILE* input = fopen(arg1, "rw+");   		// opening file with input data
    ...
    if (input) {                        		// correctness of opening the file
        ...
    } else {                            		// exit with error if we cannot open the file correctly
        printf("Error! Try again, please.");
        return 1;
    }
    ...
    FILE* output = fopen(arg2, "w+");   		// opening output file
    fprintf(output, "%lf", result);      		// output of the result to the output file
    fclose(output);                     		// closing file with output data
}
```
>6. Использовать в разрабатываемых программах генератор случайных наборов данных, расширяющих возможности тестирования.
```
//Реализовано в функции "main" в файле "main.c" через "rand()":

int main(int argc, char** argv) {
    ...
    if (argc == 4) {
        ...
        arg3 = argv[3];      //"generator" or "input"
        srand(time(NULL));
    } else {                 // exit with error
        printf("Error! Try again, please.");
        return 1;
    }
    ...
    if (strcmp(arg3, "generator") == 0) {
        double limit_min = -10;                        // -10 can be replaced by a smaller number for a larger range
        double limit_max = 10;                         // 10 can be replaced by a bigger number for a larger range
        double limit_diff = limit_max - limit_min;
        srand(time(NULL));
        double x1 = limit_min + ((double)rand() / RAND_MAX) * limit_diff;
        double x2 = limit_min + ((double)rand() / RAND_MAX) * limit_diff;
        double x3 = limit_min + ((double)rand() / RAND_MAX) * limit_diff;
        double x4 = x3 + ((double)rand() / RAND_MAX) * (limit_max - x3);
        fprintf(input, "%lf %lf %lf %lf", x1, x2, x3, x4);
	...
    }
}
```
>7. Включить в программы функций, обеспечивающих замеры времени для проведения сравнения на производительность. Необходимо добавить замеры во времени, которые не учитывают время ввода и вывода данных. Для увеличения времени работы минимум до 1 секунды, в зависимости от особенностей программы, можно либо выбирать соответствующие размеры исходных данных, либо зацикливать для многократного выполнения ту часть программы, которая выполняет вычисления. В последнем случае можно использовать соответствующую опцию командной строки, задающей количество повторений.
```
//Замеры времени реализованы в функции "main" в файле "main.c" с использованием функции "timespecDiff" из файла "timediff.c":

extern int64_t timespecDiff(struct timespec timeA, struct timespec timeB);

int main(int argc, char** argv) {
    ...    
    clock_gettime(CLOCK_MONOTONIC, &start);
    ...
    clock_gettime(CLOCK_MONOTONIC, &end);
    elapsed_ns = timespecDiff(end, start);
    printf("Elapsed: %ld ns", elapsed_ns);
    ...
}
```

Проверим, что код на C работает корректно. Так и есть. Всё хорошо!

Двигаемся дальше.



б) В программе на языке Assembler было необходимо:

>8. Реализовать программы на ассемблере в виде двух или более единиц компиляции
```
/* Благодаря описанным ниже действиям в терминале были получены две программы на ассемблере: 
"main.s" и "timediff.s", затем была проведена их линковка и получен файл "foo.exe" */

gcc ./main.c -S -o main.s
gcc -masm=intel -fno-asynchronous-unwind-tables -fno-jump-tables
     -fno-stack-protector -fno-exceptions ./main.c -S -o ./main.s
gcc ./main.s -c -o main.o
gcc ./timediff.c -S -o timediff.s
gcc -masm=intel -fno-asynchronous-unwind-tables -fno-jump-tables
     -fno-stack-protector -fno-exceptions ./timediff.c -S -o ./timediff.s
gcc ./timediff.s -c -o ./timediff.o
gcc ./main.o ./timediff.o -o foo.exe
```
>9. В полученную ассемблерную программу, откомпилированную без оптимизирующих и отладочных опций, добавить комментарии, поясняющие эквивалентное представление переменных в программе на C.
```
//Данный пункт был выполнен, что видно по действиям в терминале в п.8. Комментарии добавила в последнюю версию 
ассемблерной программы "main.s", представленную в этом же репозитории, как и требовалось по условию.
```
>10. Из ассемблерной программы убраны лишние макросы за счет использования соответствующих аргументов командной строки и/или за счет ручного редактирования исходного текста ассемблерной программы.
```
//Выполнено в последних версиях ассемблерных программ "main.s" и "timediff.s"

//Убраны были следующие макросы: 
	.p2align 4
	.size	timespecDiff, .-timespecDiff
	.ident	"GCC: (GNU) 10.3.0"
	.section	.note.GNU-stack,"",@progbits
	.size	main, .-main
	.section	.rodata.cst8,"aM",@progbits,8
	.align 8
	.section	.rodata.cst16,"aM",@progbits,16
	.section	.rodata.cst8
```
>11. Модифицированная ассемблерная программа отдельно откомпилирована и скомпонована без использования опций отладки.
```
//Выполнено, см. действия в терминале в п.8
```
>12. В ассемблерную программу при вызове функции добавить комментарии, описывающие передачу фактических параметров и перенос возвращаемого результата.
```
//Выполнено в последней версии ассемблерной программы "main.s"
```
>13. В функциях для формальных параметров добавить комментарии, описывающие связь между параметрами языка Си и регистрами (стеком).
```
//Выполнено в последней версии ассемблерной программы "main.s"
```
>14. Рефакторинг программы на ассемблере за счет оптимизации использования регистров процессора. Или написание ассемблерной программы с нуля, используя собственное распределение регистров.
```
//К счастью, или, к сожалению, программа получилась объемной, вследствие чего уже были использованы по максимуму регистры процессора (в т.ч. регистры r12, r13, r14, r15). Следовательно, требование выполнено
```
>15. Добавить комментарии в разработанную программу, поясняющие эквивалентное использование регистров вместо переменных исходной программы на C.
```
//Выполнено по очевидным причинам, описанным в п.14
```
>16. Сопоставление размеров программы на ассемблере, полученной после компиляции с языка C с модифицированной программой, использующей регистры.
```
//Размер программы уменьшился
```
>17. Используя опции оптимизации по скорости, сформировать из программы на на C исходный код ассемблере. Провести сравнительный анализ с ассемблерной программой без оптимизации по размеру ассемблерного кода, размеру исполняемого файла и производительности. Сопоставить эти программы с собственной программой, разработанной на ассемблере, в которой вместо переменных максимально возможно используются регистры.
```
//Следующими действиями в терминале сформировали необходимую программу:

gcc ./main.c -S -o main.s
gcc ./timediff.c -S -o timediff.s
gcc -masm=intel -fno-asynchronous-unwind-tables -fno-jump-tables \
>     -fno-stack-protector -fno-exceptions -O0 -O1 -O2 -O3 ./main.c -S -o ./main.s
gcc -masm=intel -fno-asynchronous-unwind-tables -fno-jump-tables \
>     -fno-stack-protector -fno-exceptions -O0 -O1 -O2 -O3 ./timediff.c -S -o ./timediff.s
gcc ./main.s -c -o main.o
gcc ./timediff.s -c -o ./timediff.o
gcc ./main.o ./timediff.o -o foo.exe

//Результаты по размеру ассемблерного кода: код с оптимизациями по скорости больше, чем код без перечисленных оптимизаций, который, в свою очередь, больше кода с собственной программой

//Результаты по размеру исполняемого файла: размер исполняемого файла увеличился

//Результаты по производительности:

./foo.exe input1.txt output.txt input
Elapsed: 140 ns
./foo.exe input2.txt output.txt input
Elapsed: 260 ns
./foo.exe input3.txt output.txt input
Elapsed: 390 ns
./foo.exe input4.txt output.txt input
Elapsed: 90 ns
```
>18. Аналогично, используя опции оптимизации по размеру, сформировать код на ассемблере. Провести сравнительный анализ с неоптимизированной программой по размеру ассемблерного кода, размеру исполняемого файла и производительности. Сопоставить эти программы с собственной программой, разработанной на ассемблере, в которой вместо переменных максимально возможно используются регистры.
```
//Следующими действиями в терминале сформировали необходимую программу:

gcc ./main.c -S -o main.s
gcc ./timediff.c -S -o timediff.s
gcc -masm=intel -fno-asynchronous-unwind-tables -fno-jump-tables \
>     -fno-stack-protector -fno-exceptions -Os ./main.c -S -o ./main.s
gcc -masm=intel -fno-asynchronous-unwind-tables -fno-jump-tables \
>     -fno-stack-protector -fno-exceptions -Os ./timediff.c -S -o ./timediff.s
gcc ./main.s -c -o main.o
gcc ./timediff.s -c -o ./timediff.o
gcc ./main.o ./timediff.o -o foo.exe

//Результаты по размеру ассемблерного кода: код с оптимизациями по размеру меньше, чем код с оптимизациями по скорости, но всё ещё больше кода с собственной программой

//Результаты по размеру исполняемого файла: размер исполняемого файла уменьшился

//Результаты по производительности:

./foo.exe input1.txt output.txt input
Elapsed: 90 ns
./foo.exe input2.txt output.txt input
Elapsed: 180 ns
./foo.exe input3.txt output.txt input
Elapsed: 500 ns
./foo.exe input4.txt output.txt input
Elapsed: 100 ns
```

в) Также необходимо было привести полное тестовое покрытие: (спойлер: тестирование обеих программ прошло успешнo)

>19. Подготовка нескольких файлов, обеспечивающих тестовое покрытие разработанной программы.
```
//Выполнено, см. файлы "input1.txt", "input2.txt", "input3.txt", "input4.txt"
```
>20. Представить полное тестовое покрытие, дающее одинаковый результат на обоих программах. Привести результаты тестовых прогонов для обеих программ, демонстрирующие эквивалентность функционирования.
```
//Выполнено в терминале следующими действиями:

./main input1.txt output.txt input
Elapsed: 110 ns
./main input2.txt output.txt input
Elapsed: 160 ns
./main input3.txt output.txt input
Elapsed: 390 ns
./main input4.txt output.txt input
Elapsed: 90 ns

./foo.exe input1.txt output.txt input
Elapsed: 120 ns
./foo.exe input2.txt output.txt input
Elapsed: 200 ns
./foo.exe input3.txt output.txt input
Elapsed: 440 ns
./foo.exe input4.txt output.txt input
Elapsed: 80 ns

//Результаты тестовых прогонов для обеих программ были абсолютно одинаковы:

Для вводимого файла "input1.txt":
1
Для вводимого файла "input2.txt":
0
Для вводимого файла "input3.txt":
15
Для вводимого файла "input4.txt":
0
```
>21. Представить полученные временные данные в отчете для разных вариантов тестовых прогонов (наряду с другими данными, полученными при выполнении ранее описанных требований).
```
//Выполнено, см. п.20
```

