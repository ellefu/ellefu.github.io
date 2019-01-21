---
layout: post
title:  "Generic"
date:   2019-01-21 14:15:00 +0800
categories: Java
---
泛型在物件導向和設計模式中都有廣泛的應用

```java
import java.util.*;
public class Generic {

   public static void main(String[] args) {
        List arrayList = new ArrayList();
        arrayList.add("aaa");
        arrayList.add(100);

        for (int i =0; i<arrayList.size(); i++) {
                String item = (String) arrayList.get(i);  //crash here
                System.out.println("item = " + item);
        }
   }
}
```
crash here

```
Exception in thread "main" java.lang.ClassCastException: java.lang.Integer cannot be cast to java.lang.String
	at Generic.main(Generic.java:11)
```

ArrayList可以存在任意類型，為了在編譯階段解決這樣的問題，泛型應運而生。
更改初始化list的設定，就可以在編譯器發現問題

```java
List<String> arrayList = new ArrayList<String>();
arrayList.add(100); //complie error
```

泛型只在編譯時期有效

```java
import java.util.*;

public class Generic2 {
  public static void main(String args[]) {
        List<String> stringArrayList =  new ArrayList<String>();
        List<Integer> integerArrayList =  new ArrayList<Integer>();

        Class classStringArrayList =  stringArrayList.getClass();
        Class classIntegerArrayList = integerArrayList.getClass();

        if (classStringArrayList.equals(classIntegerArrayList)) {
                System.out.println("Generic test: the same class");
        }
  }
}
```

java的泛型只在編譯階段有效
泛型類型在邏輯上可以看成是多個不同的類型，實際上都是相同的基本類型


<H1> 泛型的使用 </H1>

泛型有三種使用方式：泛型類、泛型接口、泛型方法

<H2> 泛型類 <H2>

泛型類型用於類的定義中，被稱為泛型類。<br>
通過泛型可以完成對一組類的操作對外開放相同的接口，最典型的就是各種容器類<br>
如 List, Set, Map <br>

泛型類的基本寫法

```java
class 類名稱<泛型標識: 可以是任意標識，標識指定的泛型的類型> {
	private 泛型標識 /*(成員變量類型)*/ var;
}
```

一個最普通的泛型類:

```java
public class GenericClass {
	public static void main(String args[]) {
			    //定義泛型類，傳入泛型類型實參
			Generic<Integer> genericInteger = new Generic<Integer>(123456);
			Generic<String> genericString = new Generic<String>("key_value");
			System.out.println("key is = " + genericInteger.getKey());
			System.out.println("key is  = " + genericString.getKey());	
			
			//定義泛型類時，不一定要傳入泛型類型實參，此時在泛型類中使用泛型的方式或是成員變量定義的類型可以為任何的類型
			Generic generic = new Generic("11111");
			Generic generic1 = new Generic(4444);
			Generic generic2 = new Generic(55.55);
			Generic generic3 = new Generic(false);		
			
			System.out.println("key is = " + generic.getKey());
			System.out.println("key is  = " + generic1.getKey());
			System.out.println("key is  = " + generic2.getKey());	
			System.out.println("key is  = " + generic3.getKey());	
	}

	//此處T可以隨便寫為任意標識，如T, E, K, V等形式的參數用於表示泛型
    //在實例化泛型類時，必須指定T的具體類型
	public static class Generic<T> {
	
	        // key這個成員變量的類型為T, T的類型由外部指定
			private T key;
			
			// 泛型建構子的參數類型也為T, T的類型由外部指定
			public Generic(T key) {
					this.key =  key;
			}
			
			//泛型方法 getKey 的返回值類型為T, T的類型由外部指定
			public T getKey() {
					return key;
			}
	}
}
```

the result: <br>
key is = 123456  <br>
key is  = key_value  <br>
key is = 11111  <br>
key is  = 4444  <br>
key is  = 55.55  <br>
key is  = false  <br>

```
注意：
泛型的類型參數只能是類類型，不能是簡單類型。
不能對確切的泛型類型使用instanceof操作，編譯會出錯，如以下非法的操作：
if(ex_num instanceof Generic<Number>){   
} 
```

<H2> 泛型接口 </H2>

```java
import java.util.*;

class GeneratorInterface {
	
	public static void main (String args[]) {
		FruitGenerator fruitGenerator =  new FruitGenerator();
		System.out.println(fruitGenerator.next());
		
		FruitGenerator1 fruitGenerator1 = new FruitGenerator1();
		System.out.println(fruitGenerator1.next());
	}


	// 定義一個泛型接口
	public interface Generator<T> {
		public T next();
	}

	/*
	當實現泛型接口的類，未傳入泛型實參時, 與泛型類的定義相同，在聲明類的時候，需將泛型的聲明也一起加到類中
	即 class FruitGenerator<T> implements Generator<T> 
	如果不聲明泛型，如 class FruitGenerator implements Generator<T>，編譯器會報錯: "Unknow class"
	*/
	public static class FruitGenerator<T> implements Generator<T> {
		@Override
		public T next() {
			return null;
		}
	}

	/*
	當實現泛型接口的類，傳入泛型實參時：
	定義一個生產器實現這個接口，雖然我們只創建了一個泛型接口Generator<T>
	但是我們可以為T傳入無數個實參，形成無數種類型的Generator接口
	在實現類實現泛型接口時，如已將泛型類型傳入實參類型，則所有使用泛型的地方都要替換成傳入的實參類型
	即 Generator<T>, 
	public T next(); 中的T都要換成String 類型
	public String next();
	*/
	public static class FruitGenerator1 implements Generator<String> {
		private String[] fruits = new String[]{"Apple", "Banana", "Pear"};
		@Override
		public String next() {
			Random rand = new Random();
			return fruits[rand.nextInt(3)];
		}
	}
}
```

<h2> 泛型通配符 </h2>

我們知道 Integer 是 Number 的一個子類 <br>
也驗證過 Generic<Integer> 和 Generic<Number> 實際上都是相同的基本類型 <br>
那麼在使用 Generic<Number> 作為形參的方法中，能否使用 Generator<Integer> 的實例傳入? <br>
在邏輯上類似於 Generic<Number> 和 Generic<Integer> 是否可以看到具有繼承關係的泛型類型? <br>

```
import java.util.*;

class GenericInheritance {

	public static void main(String args[]) {
		Integer integerTest = 123;
		Generic<Integer> gInteger = new Generic<Integer>(integerTest) ;
		Generic<Number> gNumber = new Generic<Number>(456);
		
		showKeyValue(gInteger);   
		//  GenericInheritance.java:10: error: incompatible types: Generic<Integer> cannot be converted to Generic<Number>
		showKeyValue(gNumber);
	}
	
	public static void showKeyValue(Generic<Number> obj) {
		System.out.println("key value is " + obj.getKey());
	}
	
	public static class Generic<T> {
			private T key;
			
			public Generic(T key) {
					this.key =  key;
			}
			public T getKey() {
					return key;
			}
	}
}
```

我們需要一個在邏輯上可以同時是 Generic<Integer> 和 Generic<Number> 父類的引用類型，<br>
因此類型通配符應運而生 <br>

```
import java.util.*;

class GenericInheritance {

	public static void main(String args[]) {
		Integer integerTest = 123;
		Generic<Integer> gInteger = new Generic<Integer>(integerTest) ;
		Generic<Number> gNumber = new Generic<Number>(456);
		
		showKeyValue(gInteger); 
		showKeyValue(gNumber);
	}
	
	// 類型通配符一般是使用 ? 代替具體的類型實參
	// 也就是說，此處的 ? 和 Number, String, Integer 一樣都是一種實際的類型
	// 可以把 ? 看成所有類型的父類。是一種真實的類型
	// 可以解決當具體類型不確定的時候，這個通配符就是 ?
	// 當操作類型時，不需要使用類型的具體功能，只使用Object類中的功能，那麼可以用 ? 通配符來表示未知類型
	public static void showKeyValue(Generic<?> obj) {
		System.out.println("key value is " + obj.getKey());
	}
	
	public static class Generic<T> {
			private T key;
			
			public Generic(T key) {
					this.key =  key;
			}
			public T getKey() {
					return key;
			}
	}
}
```











