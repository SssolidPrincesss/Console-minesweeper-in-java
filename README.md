# Console-minesweeper-in-java
В этом "гайде" я хочу разобрать код консольного сапера.   
Ход игры выглядит примерно так:    
![Menu](https://github.com/SssolidPrincesss/Console-minesweeper-in-java/blob/main/Consoleminesweeper/Game.png)  
Сначала на консоль выводятся правила самой игры, затем игроку предлагается ввести размеры поля. После чего выводится подсказка (которая помогает тестировать игру и которую можно убрать для итоговой версии этой игры).   
  
Еще на консоль выводится индексированное поле для того, чтобы подсказать игроку, как сделать ход, а также для удобства ориентирования. После того как игрок сделал ход, программа проверяет, не наступил ли он на мину.  
  
Если игрок не наступил на мину, программа рассчитывает количество мин в соседних ячейках и выводит поле с ходом игрока. И так, пока игрок не наступит на мину или на поле не останется свободных ячеек. После завершения работы главного игрового цикла (об этом напишу дальше) на консоль выводится либо сообщение о том, что игрок попал на мину, либо сообщение об окончании игры и количество ходов, сделанных игроком в обоих случаях.   



      
Теперь разберем план всего кода, какие классы и их методы за что отвечают.  
  
Данный код содержит 3 класса:  
  
1.	MinerGame.
Этот класс – тело всей игры.
Он содержит главный метод и три вспомогательных метода:  
•	main    
•	setResultOfTry  
•	fillTheField  
•	checkAdjacentField  
  
   
2.	GameHelper  
Это вспомогательных класс, который выводит индексированное поле, проверяет, наступил игрок на мину или нет, выводит поле с ходами и проверяет, содержатся ли в поле свободные ходы  
•	matrixIndex   
•	mineCheck  
•	CheckArrConainsDark  
  
   
3.	MessageHelper  
Это класс, не относящийся к логике игры, который выводит правила игры в начале и берет из класса GameHelper и его метода CheckArrConainsDark количество ходов игрока и выводит их после сообщения об окончании игры. Он не стоит отдельного внимания.

    

И наконец, код самой игры в том же порядке, как в плане:    
  
1.	MinerGame.
	  
Разберем каждую часть этого класса более подробно:   
  
Сначала создается ссылка на объект типа сканер. Он нужен для того, чтобы считать ход игрока.   
Также создается ArrayList, который содержит в себе двумерные массивы символьного типа. Он нужен для работы главного цикла.    
     
```java
public static void main(String[] args) {
public static Scanner sc = new Scanner(System.in);
	
public static ArrayList <char[][]> resultOfTry = new ArrayList<char[][]>();
```
  
   
•	main  
Главный метод, в котором находится главный цикл (в моем случае do-while, т. к. сначала игрок делает ход, а потом уже происходит проверка на мину и количество свободных ходов). 
  
В первую очередь здесь создается ссылка на объект MessageHelper, а затем вызывается его метод, отвечающий за приветствие и правила.  
```java
		MessageHelper mh = new MessageHelper();
		mh.gameRules();
```  
Затем игроку дается возможность задать размеры игрового поля:  
```java
		System.out.println("Введи размер игрового поля, чтобы начать игру:");
		int field_i = sc.nextInt();
		int field_j = sc.nextInt();
```
После чего создается двумерный символьный массив, который сразу же передается вместе с размерами поля в метод fillTheField, который случайным образом заполняет поле минами и свободными ходами:
```java
		char[][] field = new char[field_i][field_j];
		             
		fillTheField(field,field_i,field_j);
```
Затем создаются две переменные типа boolean: первая нужна для проверки наличия в поле свободных ходов, а вторая — для того, чтобы проверить, не наступил ли игрок на мину:   
```java
		boolean ifFieldContainsDark = true;
		boolean ifHitMine = true;
```  
Еще создается целочисленная переменная для подсчитывания количества ходов игрока:  
```java
		int tryCounter = 0;
```
Далее начинается главный цикл игры:
```java
		do{
```  
Здесь создается ссылка на объект типа GameHelper, а после булевое значение, возвращаемое данным методом mineCheck, присваивается ранее созданной переменной.  
Параллельно с этим вызывается сам метод mineCheck, функционал которого разберу позже.  
```java
			GameHelper gh = new GameHelper();
			gh.matrixIndex(field_i, field_j);
			ifHitMine = gh.mineCheck(field, field_i,field_j);
```
Также здесь вы можете наблюдать закомментированную часть кода:
```java
/* подсказка разработчику
		       for (char[][] array2D : resultOfTry) {
		            System.out.println("Next 2D ЕКНarray:");
		            for (char[] row : array2D) {
		                for (char element : row) {
		                    System.out.print(element + " ");
		                }
		                System.out.println(); 
		            }
		            System.out.println(); 
		            
		        }
*/
```
Это подсказка для разработчика, опять же для удобства тестирования, если ее раскомментировать, на консоль будет выведена вот такая штука:  

![Menu](https://github.com/SssolidPrincesss/Console-minesweeper-in-java/blob/main/Consoleminesweeper/ConsoleHintOnly.png)  

Эта подсказка отражает актуальную ситуацию на поле.  

После этого второй булевой переменной присваивается значение, возвращаемое методом checkArrContainsDark (проверяет свободные ходы).  
В самом конце тела главного цикла увеличивается счетчик ходов.
```java
			ifFieldContainsDark = gh.CheckArrConainsDark(resultOfTry);
		    	tryCounter++; 
```
Условием работы главного цикла игры является наличие свободных ходов и то, что игрок не сходил ни мину:
```java
		}while(ifHitMine == true && ifFieldContainsDark == true);
```
И в самом конце метода очищается лист, и вызывается метод, отвечающий за завершение игры:
```java
		resultOfTry.clear();
		mh.finishGame(tryCounter);
```

    
•	setResultOfTry  
Этот метод принимает значение Эррэй-Листа из вспомогательного класса GameHelper и передает содержание этого листа в лист главного класса. Это нужно для того, чтобы проверить наличие в игровом поле свободных ходов:
```java
public void setResultOfTry(ArrayList<char[][]> rt){
        for (char[][] element : rt) {
        	resultOfTry.add(element);
        }
}
```	
•	fillTheField  
Этот метод очень важная часть логики игры. Он генерирует мины и свободные ходы на поле в случайном порядке (в зависимости от времени):  

```java
	public static void fillTheField (char[][] matrix,int mas_i,int mas_j){
		Random rnd = new Random(System.currentTimeMillis());
		for (int i=0;i<mas_i;i++){
		    for (int j=0;j<mas_j;j++){
		        if (rnd.nextInt(2)==0) matrix[i][j]='.';
		        else matrix[i][j]='*';
		        System.out.print(matrix[i][j] + "    ");
		    }
		    System.out.println();
		    System.out.println();
		}
	}
```
•	checkAdjacentField  
Это последний метод данного класса и он проверяет наличие в соседних от хода игрока клетках поля наличие мин и считает их количество, а после возвращает это значение в символьном типе:

```java
	public static char adjacentCellsMines(int i, int j, char[][] pt) {
		int mineCounter = 0;
		if (j <= pt[i].length - 2) { 
			if(pt[i][j + 1] == '*') {
				mineCounter++;
			}
		}
		if (j <= pt[i].length - 2 && i <= pt.length - 2) { 
			if(pt[i + 1][j + 1] == '*') {
				mineCounter++;
			}	
		}
		if (j <= pt[i].length - 2 && i > 0) { 
			if(pt[i - 1][j + 1] == '*') {
				mineCounter++;
			}
		}
		if (j > 0) { 
			if(pt[i][j - 1] == '*') {
				mineCounter++;
			}
		}
		if (j > 0 && i <= pt.length - 2) { 
			if(pt[i + 1][j - 1] == '*') {
				mineCounter++;
			}
		}
		if (j > 0 && i > 0) { 
			if(pt[i - 1][j - 1] == '*') {
				mineCounter++;
			}
		}
		if (i <= pt.length - 2) { 
			if(pt[i + 1][j] == '*') {
				mineCounter++;
			}
		}
		if (i > 0) { 
			if(pt[i - 1][j] == '*') {
				mineCounter++;
			}
		}
		String stringValue = String.valueOf(mineCounter);
		return stringValue.charAt(0);
	}
}
```
Главный класс выводит на консоль (то, что он задействует другие классы для вывода остальной информации, в расчет не берем):  
![Menu](https://github.com/SssolidPrincesss/Console-minesweeper-in-java/blob/main/Consoleminesweeper/MinerGameOut.png)  
  
 


    
2.	GameHelper    

В первую очередь необходимо похаботиться о возможности считать шаг игрока, в этом нам поможет сканер:  
```java
public static Scanner scanTry = new Scanner(System.in);
```
•	matrixIndex  
У этого метода достаточно простая задача – он выводит на консоль индексированное поле, чтобы игроку было понятно, как вводить свой ход.   
```java
	public void matrixIndex(int mi, int mj){
		String[][] masivPlay = new String[mi][mj];
		
		System.out.println("Выбери один из индексов:");
		
    	for (int i = 0; i < mi; i++) {
  	      for (int j = 0; j < mj; j++) {
  	            	masivPlay[i][j] = "" + i + " " + j + "";
  	      }
    	}
  	             
    	for (int i = 0; i < mi; i++) {
    		for (int j = 0; j < mj; j++) {
  	           System.out.print(masivPlay[i][j] + "  ");
    		}
    		System.out.println();
    		System.out.println();
    	}
    	System.out.println("");
    }
```
Теперь разберем подробно каждый метод:  
  
•	mineCheck  
А вот этот метод имеет достаточно широкий функционал.  
В первую очередь создается булеваая переменная (нужна для работы главного цикла) и считывает ход игрока:    
```java
boolean explosionСhecking  = true;

System.out.println("Введи свой выбор:");
    	
        int try_i = scanTry.nextInt();
        int try_j = scanTry.nextInt();
```  
Потом создается ссылка на объект типа MinerGame для дальнейшей работы с методами этого класса, а также создаются временные массив и лист для хранения «буферных данных»:  
```java
 	MinerGame mg = new MinerGame();

        char[][] fTemp = new char[mas_i][mas_j];
        
        ArrayList <char[][]> tempArray = new ArrayList<char[][]>();
```   
Дальше идет условие, которое проверяет, не наступил ли игрок на мину. Если наступил, то массив заполняется точками при помощи первого цикла:
```java
	if (ff[try_i][try_j] == '*') {
        	System.out.println("Сапер взлетел на воздух!");
        	
        	
        	for (int i = 0; i < ff.length; i++) {
                for (int j = 0; j < ff[i].length; j++) {
                	fTemp[i][j] = '.';
                }
            } 
            
        	System.out.println();
```
А после при помощи второго массива проверяет предыдущие ходы игрока и добавляет их на поле:  
```java
		for (int i = 0; i < ff.length; i++) {
        		for (int j = 0; j < ff[i].length; j++) {
        			if (ff[i][j] == '0' || ff[i][j] == '1' || ff[i][j] == '2' || ff[i][j] == '3' || ff[i][j] == '4' || ff[i][j] == '5' || ff[i][j] == '6' || ff[i][j] == '7' || ff[i][j] == '8') {
        				fTemp[i][j] = ff[i][j];
        			}
        		}     
        	}
```
В конце на место хода игрока ставится мина ('*') и булевой переменной присваивается false: 
```java
        	fTemp[try_i][try_j] = '*'; 
        	
        	explosionСhecking = false;
        }
```   
Если же игрок не попал на мину, то сначала массив заполняется точками:  
```java
        else {
            for (int i = 0; i < ff.length; i++) {
                for (int j = 0; j < ff[i].length; j++) {
                	fTemp[i][j] = '.';
                }
            } 
            
        	System.out.println();
```
Потом идет цикл с двумя условиями:
1. Условие, которое проверяет соответствие индексов передаваемого в метод массива с индексами, введенными игроком (ход игрока). Если совпадает, то при помощи метода checkAdjacentField из главного класса элементу из передаваемого массива присваивается символьное значение (количество мин рядом).
2. Условие, которое проверяет предыдущие ходы игрока и добавляет их на поле
```java
        	for (int i = 0; i < ff.length; i++) {
        		for (int j = 0; j < ff[i].length; j++) {
        			if ( i == try_i && j == try_j) {
        				ff[i][j] = mg.adjacentCellsMines(try_i, try_j, ff);
        				fTemp[i][j] = ff[i][j];
        			}
        			if (ff[i][j] == '0' || ff[i][j] == '1' || ff[i][j] == '2' || ff[i][j] == '3' || ff[i][j] == '4' || ff[i][j] == '5' || ff[i][j] == '6' || ff[i][j] == '7' || ff[i][j] == '8') {
        				fTemp[i][j] = ff[i][j];
        			}
        		}     
        	}
```
И в конце булевой переменной присваивается true:  
```java
explosionСhecking = true;
```  
Далее в методе размещен цикл, который просто выводит массив с попытками на консоль:
```java
        for (int i = 0; i < ff.length; i++) {
            for (int j = 0; j < ff[i].length; j++) {
                System.out.print(fTemp[i][j] + "    "); 
            }
            System.out.println();
            System.out.println();
        }
```
В самом конце метода используется подготовленный ранее временный лист, в который мы добавляем массив с предыдущими ходами игрока и при помощи метода setResultOfTry передаем в главный класс для дальнейшей обработки.  
После необходимо очистить лист и вернуть булевое значение.  
```java
        tempArray.add(ff);
        mg.setResultOfTry(tempArray);
        tempArray.remove(ff);
        
        return explosionСhecking;
    }
```  
  
•	CheckArrConainsDark  
Этот метод проверяет наличие свободных ходов в игровом поле и возвращает булевое значение, которое также важно для работы главного цикла:
```java
public boolean CheckArrConainsDark(ArrayList <char[][]> l){
    	boolean d = true;
    	int countDark = 0;
    	for (char[][] array : l) {
            for (char[] row : array) {
                for (char element : row) {
                    if (element == '.' ) {
                    	countDark++;   
                    }
                }
            }
        }
    	if (countDark >= 1) {
    		d = true;
    	}
    	else {
    		d = false;
    	}
    	return d;
    }
    
}
```
Этот кусок кода выводит на консоль индексированное поле, а также результат попыток игрока:  
![Menu](https://github.com/SssolidPrincesss/Console-minesweeper-in-java/blob/main/Consoleminesweeper/GameHelperOut.png)  

Если игрок сделал ход на мину:  
![Menu](https://github.com/SssolidPrincesss/Console-minesweeper-in-java/blob/main/Consoleminesweeper/UnluckTry.png)

3.	MessageHelper
```java
public class MessageHelper {
	public void gameRules() {
		System.out.print("Добро пожаловать в игру сапер!");
		System.out.println("Правила игры: игроку необходимо открыть все ячейки поля, не задев мину");
		System.out.println("Игровое поле не может превышать размер 10 на 10 ячеек");
		System.out.println("Удачи");
		System.out.println();
	}
	
	public void finishGame(int tk) {
		System.out.println("Игра окончена");
		System.out.println("Ты смог сделать " + tk + " шаг(а/ов)" );
	}
}
```    
Эта часть кода выводит на консоль правила игры:  
![Menu](https://github.com/SssolidPrincesss/Console-minesweeper-in-java/blob/main/Consoleminesweeper/GameRules.png)  
А также оповещает об окончании игры и показывает количество попыток игрока:  
![Menu](https://github.com/SssolidPrincesss/Console-minesweeper-in-java/blob/main/Consoleminesweeper/EndGame.png)  


Код нуждается в серьезной доработке: в нем не соблюдены принципы инкасуляции, очень много странных решений или вовсе "костьлей".  
Разобранная игра является демо-версией нормального «Сапера» по двум причинам:     
Во-первых, тут нет графического интерфейса, из-за чего сложнее ориентироваться на поле, а также нет возможности пометить флажком расположение мин.     
Во-вторых, данная игра запускается только из компилятора.  
  
  
В будущем я бы хотел сделать код более "чистым", дополнить эту игру графическим интерфейсом в необычном оформлении, возможностью пройти игру на сайте, а также как фишку добавить возможность создавать не только четырехугольные поля ("необычная" форма поля будет даваться игроку на выбор, в отличие от обычного четырехугольного).

Ниже можно посмотреть полный код игры:


```java
import java.util.*;

public class MinerGame {
	
	public static Scanner sc = new Scanner(System.in);
	
	public static ArrayList <char[][]> resultOfTry = new ArrayList<char[][]>();
	
	public static void main(String[] args) {
		MessageHelper mh = new MessageHelper();
		mh.gameRules();
		
		System.out.println("Введи размер игрового поля, чтобы начать игру:");
		int field_i = sc.nextInt();
		int field_j = sc.nextInt();
		            
		char[][] field = new char[field_i][field_j];
		             
		fillTheField(field,field_i,field_j);
		
		System.out.println("");

		boolean ifFieldContainsDark = true;
		boolean ifHitMine = true;
		
		int tryCounter = 0;
		
		
		do{
		    GameHelper gh = new GameHelper();
			gh.matrixIndex(field_i, field_j);
			ifHitMine = gh.mineCheck(field, field_i,field_j);
			
/* подсказка разработчику
		       for (char[][] array2D : resultOfTry) {
		            System.out.println("Next 2D ЕКНarray:");
		            for (char[] row : array2D) {
		                for (char element : row) {
		                    System.out.print(element + " ");
		                }
		                System.out.println(); 
		            }
		            System.out.println(); 
		            
		        }
*/
			
			ifFieldContainsDark = gh.CheckArrConainsDark(resultOfTry);
		    tryCounter++; 
		 }while(ifHitMine == true && ifFieldContainsDark == true);
		resultOfTry.clear();
		mh.finishGame(tryCounter);
	}

	
	public void setResultOfTry(ArrayList<char[][]> rt){
        for (char[][] element : rt) {
        	resultOfTry.add(element);
        }
	}
	
	
	
	public static void fillTheField (char[][] matrix,int mas_i,int mas_j){
		Random rnd = new Random(System.currentTimeMillis());
		for (int i=0;i<mas_i;i++){
		    for (int j=0;j<mas_j;j++){
		        if (rnd.nextInt(2)==0) matrix[i][j]='.';
		        else matrix[i][j]='*';
		        System.out.print(matrix[i][j] + "    ");
		    }
		    System.out.println();
		    System.out.println();
		}
	}
		 
		    
		    
public static char adjacentCellsMines(int i, int j, char[][] pt) {
		int mineCounter = 0;
		if (j <= pt[i].length - 2) { 
			if(pt[i][j + 1] == '*') {
				mineCounter++;
			}
		}
		if (j <= pt[i].length - 2 && i <= pt.length - 2) { 
			if(pt[i + 1][j + 1] == '*') {
				mineCounter++;
			}	
		}
		if (j <= pt[i].length - 2 && i > 0) { 
			if(pt[i - 1][j + 1] == '*') {
				mineCounter++;
			}
		}
		if (j > 0) { 
			if(pt[i][j - 1] == '*') {
				mineCounter++;
			}
		}
		if (j > 0 && i <= pt.length - 2) { 
			if(pt[i + 1][j - 1] == '*') {
				mineCounter++;
			}
		}
		if (j > 0 && i > 0) { 
			if(pt[i - 1][j - 1] == '*') {
				mineCounter++;
			}
		}
		if (i <= pt.length - 2) { 
			if(pt[i + 1][j] == '*') {
				mineCounter++;
			}
		}
		if (i > 0) { 
			if(pt[i - 1][j] == '*') {
				mineCounter++;
			}
		}
		String stringValue = String.valueOf(mineCounter);
		return stringValue.charAt(0);
	}
}
```


```java
import java.util.*;

public class GameHelper {
	
	public static Scanner scanTry = new Scanner(System.in);
	
	
	
    public void matrixIndex(int mi, int mj){
		String[][] masivPlay = new String[mi][mj];
		
		System.out.println("Выбери один из индексов:");
		
    	for (int i = 0; i < mi; i++) {
  	      for (int j = 0; j < mj; j++) {
  	            	masivPlay[i][j] = "" + i + " " + j + "";
  	      }
    	}
  	             
    	for (int i = 0; i < mi; i++) {
    		for (int j = 0; j < mj; j++) {
  	           System.out.print(masivPlay[i][j] + "  ");
    		}
    		System.out.println();
    		System.out.println();
    	}
    	System.out.println("");
    }
	

	


    public  boolean mineCheck(char[][] ff, int mas_i, int mas_j){
  
    	boolean explosionСhecking  = true;
    	
    	System.out.println("Введи свой выбор:");
    	
        int try_i = scanTry.nextInt();
        int try_j = scanTry.nextInt();
        
        MinerGame mg = new MinerGame();

        char[][] fTemp = new char[mas_i][mas_j];
        
        ArrayList <char[][]> tempArray = new ArrayList<char[][]>();
        
        
        
        if (ff[try_i][try_j] == '*') {
        	System.out.println("Сапер взлетел на воздух!");
        	
        	
        	for (int i = 0; i < ff.length; i++) {
                for (int j = 0; j < ff[i].length; j++) {
                	fTemp[i][j] = '.';
                }
            } 
            
        	System.out.println();
        	
        	for (int i = 0; i < ff.length; i++) {
        		for (int j = 0; j < ff[i].length; j++) {
        			if (ff[i][j] == '0' || ff[i][j] == '1' || ff[i][j] == '2' || ff[i][j] == '3' || ff[i][j] == '4'
        					|| ff[i][j] == '5' || ff[i][j] == '6' || ff[i][j] == '7' || ff[i][j] == '8') {
        				fTemp[i][j] = ff[i][j];
        			}
        		}     
        	}
        	fTemp[try_i][try_j] = '*'; 
        	
        	explosionСhecking = false;
        }
        else {
            for (int i = 0; i < ff.length; i++) {
                for (int j = 0; j < ff[i].length; j++) {
                	fTemp[i][j] = '.';
                }
            } 
            
        	System.out.println();
        	
        	for (int i = 0; i < ff.length; i++) {
        		for (int j = 0; j < ff[i].length; j++) {
        			if ( i == try_i && j == try_j) {
        				ff[i][j] = mg.adjacentCellsMines(try_i, try_j, ff);
        				fTemp[i][j] = ff[i][j];
        			}
        			if (ff[i][j] == '0' || ff[i][j] == '1' || ff[i][j] == '2' || ff[i][j] == '3' || ff[i][j] == '4'
        					|| ff[i][j] == '5' || ff[i][j] == '6' || ff[i][j] == '7' || ff[i][j] == '8') {
        				fTemp[i][j] = ff[i][j];
        			}
        		}     
        	}
            explosionСhecking = true;
        }
 
        
        for (int i = 0; i < ff.length; i++) {
            for (int j = 0; j < ff[i].length; j++) {
                System.out.print(fTemp[i][j] + "    "); 
            }
            System.out.println();
            System.out.println();
        }
        
        tempArray.add(ff);
        mg.setResultOfTry(tempArray);
        tempArray.remove(ff);
        
        return explosionСhecking;
    }
    
    
    
    
    public boolean CheckArrConainsDark(ArrayList <char[][]> l){
    	boolean d = true;
    	int countDark = 0;
    	for (char[][] array : l) {
            for (char[] row : array) {
                for (char element : row) {
                    if (element == '.' ) {
                    	countDark++;   
                    }
                }
            }
        }
    	if (countDark >= 1) {
    		d = true;
    	}
    	else {
    		d = false;
    	}
    	return d;
    }
    
}
```


      
```java
public class MessageHelper {
	public void gameRules() {
		System.out.print("Добро пожаловать в игру сапер!");
		System.out.println("Правила игры: игроку необходимо открыть все ячейки поля не задев мину");
		System.out.println("Игровое поле не может привышать размер 10 на 10 ячеек");
		System.out.println("Удачи");
		System.out.println();
	}
	
	public void finishGame(int tk) {
		System.out.println("Игра окончена");
		System.out.println("Ты смог сделать " + tk + " шаг(а/ов)" );
	}
}
```  



  
     
