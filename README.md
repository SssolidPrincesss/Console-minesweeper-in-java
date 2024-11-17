# Console-minesweeper-in-java
В этом "гайде" я хочу раззобрать код консольного сапера.   
Ход игры выглядит примерно так:    
![Menu](https://github.com/SssolidPrincesss/Console-minesweeper-in-java/blob/main/Consoleminesweeper/game.png)  
Сначала на консоль выводятся правила самой игры, затем игроку предлагается ввести размеры поля. После чего выводится подсказка(которая помогает тестировать игру и которю можно убрать для итоговой версии этой игры). Так же на консоль выводится индексированное поле, для того, чтобы подсказать игроку как сделать ход, а так же для удобства ариентирования. После того как игрок сделал ход программа проверяет не наступил ли он на мину. Если не наступил программа рассчитывает колличество мин в соседних ячейках и выводит поле с ходом игрока. И так пока игрок не наступит на мину, или на поле не останется свободных ячеек. После завершения работы главного игрового цикла(об этом напишу дальше) на консоль выводиться либо сообщение о том, что  игрок попал на мину, либо сообщение об окончании игры и количество ходов, сделанных игрокм в обоих случаях.   

Теперь разберем план всего кода, какие классы и тх методы за что отвечают.  
  
Данный код содержит 3 класса:
1.	MinerGame.
Этот класс – тело всей игры
Он содержит главный метод и три вспомогательных метода:  
•	main  
Главный класс, в котором находится главный цикл(в моем случае do while, т.к сначала игрок делает ход, а потом уже происходит проверка на мину и количество свободных ходов  
•	setResultOfTry   
Этот метод принимает значение ЭррэйЛиста из вспомогательного класса GameHelper и передает содержание этого листа в лист главного класса. Это нужно для того, чтобы проверить наличие в игровом поле свободных ходов  
•	fillMatrix  
Этот метод очень важная часть логики игры. Он генерирует мины и свободные ходы на поле в случайном порядке(в зависимости от времени)   
•	checkAdjacentField  
Этот метод проверяет наличие в соседних от хода игрока клетках поля наличие мин и считает их количество, а после  возвращает это значение в символьном типе  
2.	GameHelper  
Это вспомогательных класс, который выводит индексированное поле, проверяет наступил игрок на мину или нет, выводит поле с ходами и проверяет содержатся ли в поле свободные ходы   
•	matrixIndex  
У этого метода достаточно простая задача – он выводит на консоль индексированное поле, чтобы игроку было понятно, как вводить свой ход  
•	matrixIndex  
А вот этот метод имеет достаточно широкий функционал.  
В первую очередь он проверяет не наступил ли игрок на мину, и если этого не случилось, он создает массив только из точек, чтобы игрок не видел где мины
После он при помощи метода checkAdjacentField из главного класса добавляет в поле на место хода игрока количество мин в соседних ячейках.
Затем при помощи вспомогательного листа и такого же вспомогательного метода setResultOfTry передает измененный массив(игровое поле) в главный класс для дальнейшей проверки в главном цикле  
Так же он возвращает булевое значение, которое нужно для работы главного цикла  
•	CheckArrConainsDark  
Этот метод проверяет наличие свободных ходов в игровом поле и возвращает булевое значение, которое так же важно для работы главного цикла  
3.	MessageHelper  
Это класс, не относящийся к логике игры, который выводит правила игры в начале и берет из класса GameHelper и его метода CheckArrConainsDark колличество ходов игрока и выводит их после сообщения об окончании игры. Он не стоит отдельного внимания.


И наконец код самой игры в том же порядке как в плане:   
1.	MinerGame.
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
		 
		    
		    
	public static char checkAdjacentField(int i, int j, char[][] pt) {
		int count = 0;
		if (j <= pt[i].length - 2) { // to the right
			count += pt[i][j + 1] == '*' ? 1 : 0;
		}
		if (j <= pt[i].length - 2 && i <= pt.length - 2) { // move to top right
			count += pt[i + 1][j + 1] == '*' ? 1 : 0;
		}
		if (j <= pt[i].length - 2 && i > 0) { // move to bottom right
		    count += pt[i - 1][j + 1] == '*' ? 1 : 0;
		}
		if (j > 0) { // to the left
		    count += pt[i][j - 1] == '*' ? 1 : 0;
		}
		if (j > 0 && i <= pt.length - 2) { // to top left
		    count += pt[i + 1][j - 1] == '*' ? 1 : 0;
		}
		if (j > 0 && i > 0) { // to bottom left
		    count += pt[i - 1][j - 1] == '*' ? 1 : 0;
		}
		if (i <= pt.length - 2) { // move to top
		    count += pt[i + 1][j] == '*' ? 1 : 0;
		}
		if (i > 0) { // move top bottom
		    count += pt[i - 1][j] == '*' ? 1 : 0;
		}
		String stringValue = String.valueOf(count);
		return stringValue.charAt(0);
	}
}
```
Этот кусок кода выводит на консоль:  
![Menu](https://github.com/SssolidPrincesss/Console-minesweeper-in-java/blob/main/Consoleminesweeper/MinerGameOut.png)  
  
Так же здесь вы можете наблюдать закомментирванную часть кода, это подсказка для разработчика, опять же для удобства тестирования, если ее раскомментировать, на консоль будет выведено:  

![Menu](https://github.com/SssolidPrincesss/Console-minesweeper-in-java/blob/main/Consoleminesweeper/ConsoleHintOnly.png)  

При каждом ходе будет выводиться актуальная ситуация на поле 


    
2.	GameHelper    
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
        				ff[i][j] = mg.checkAdjacentField(try_i, try_j, ff);
        				fTemp[i][j] = ff[i][j];
        			}
        		}     
        	}

            for (int i = 0; i < ff.length; i++) {
                for (int j = 0; j < ff[i].length; j++) {
                    System.out.print(fTemp[i][j] + "    "); 
                }
                System.out.println();
                System.out.println();
            }
            explosionСhecking = true;
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


3.	MessageHelper
```java
public class MessageHelper {
	public void gameRules() {
		System.out.print("Добро пожаловать в игру сапер!");
		System.out.println("Правила игры: игроку необходимо открыть все ячейки поля не задев мину");
		System.out.println("Но! Есть одна особенность");
		System.out.println("Ты будешь видеть только последний ход");
		System.out.println("Удачи");
		System.out.println();
	}
	
	public void finishGame(int tk) {
		System.out.println("Игра окончена");
		System.out.println("Ты смог сделать " + tk + " шаг(а/ов)" );
	}
}
```    
