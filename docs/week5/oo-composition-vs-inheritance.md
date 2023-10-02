---
layout: default
title : Woche 5
---
# Vererbung und Objektkomposition

*Neben der "uses" und der "is_composed_of" Beziehung, spielt in der objektorientierten Programmierung noch eine andere Beziehung eine vermeintlich wichtige Rolle, nämlich die "inherits_from" Beziehung. In diesem Artikel schauen wir uns diese Beziehung näher an.*

## Die "inherits_from" Beziehung

Gegeben zwei Module (Klassen) A und B. Objektorientierte Programmiersprachen geben uns ein Sprachkonstrukt um die Beziehung B "inherits_from" A zu definieren. 
Wir sagen, *Klasse B spezialisiert Klasse A*, oder  *Klasse A generalisiert Klasse B*. 
In diesem Kontext wird A auch als Superklasse und B als Subklasse bezeichnet. 
Diese Beziehung bedeutet, dass das Modul B alle Eigenschaften von Modul A hat sowie alle Funktionalität auch anbietet, 
zusätzlich aber Modul A auch noch um eigene Eigenschaften und Funktionalität erweitern kann.

Wir schauen uns zuerst ein Beispiel an:
Angenommen wir haben folgende Klasse ```Employee``` die relevante Aspekte von Mitarbeitern im Rahmen einer Mitarbeiterverwaltungssoftware modelliert.
```java
class Employee {
  public final String firstName;
  public final String lastName;
  public final Integer age;
  public void hire(String firstName, String lastName, Integer age) { /* Implementation */ }
  public void fire() { /* Implementation */ };
}
```
Die hier modellierten Attribute gelten für alle Mitarbeiter. Nun gibt es jedoch verschiedene Arten von Mitarbeitern, die jeweils noch zusätzliche Eigenschaften haben. 
Mittels Vererbung können wir nun diese zusätzlichen Eigenschaften modellieren, indem 
wir die allgemeinen Eigenschaften, die wir bereits in der Klasse ```Employee``` definiert haben, erben.

```java
class AdministrativeStaff extends Employee {
  void doThis() { /** Implementation */ }
}
```
```java
class TecnicalStaff extends Employee {
  void addSkill(Skill skill) { /** Implementation */ }
}
```

## Schnittstellenvererbung und Implementationsvererbung

Die meisten objektorientierten Programmiersprachen unterstützen  zwei Varianten des Vererbungskonzepts. Die erste Variante ist die *Schnittstellenvererbung*. Dabei wird bei der Vererbung einfach garantiert, dass die Subklasse alle Methoden und Attribute, die in der 
Schnittstelle definiert sind, unterstützt. Es werden jedoch keine Implementationen von der Superklasse vererbt. In der Programmiersprache Java wird Schnittstellenvererbung durch das Konstrukt ```Interface``` unterstützt, dass dann von den Klassen implementiert wird. Dieses Konzept ist enorm wichtig und führt zu flexiblem und einfach zu erweiterbarem Code.

Bei der *Implementationsvererbung* wird zusätzlich auch die Implementation der Superklasse mitvererbt. Die Subklasse muss also nur noch eine Implementation für die neuen, nicht bereits in der Superklasse implementierten Methoden, bereitstellen.

Auf den ersten Blick erscheint die Implementationsvererbung eine gute Idee zu sein. Die Subklasse muss weniger Code implementieren und entsprechend sollten weniger Fehler passieren. 
Leider führt die Implementationsvererbung aber auch zu einer starken Kopplung der Subklasse an die Superklasse, da durch diese Art der Vererbung Implementationsdetails der Superklasse mitvererbt werden. Das Prinzip vom *Information Hiding* wird hier also verletzt. Deshalb wird häufig davon abgeraten, diese Art der Vererbung zu verwenden. 



## Implementationsvererbung versus Komposition

Im Folgenden illustrieren wir das Problem der Implementationsvererbung anhand eines konkreten Beispiels. Wir zeigen auch, wie wir mit Komposition von Objekten ein besseres Design erhalten, ohne dass wir Code duplizieren müssen. 

Dazu schauen wir uns folgende Klassendefinition an
```java
public class CountingList<T> extends ArrayList<T> {
        private int counter = 0;
       
        @Override
        public void add(T elem) {
          super.add(elem);
          counter++;
        }
       
        @Override
        public void addAll(Collection<T> other) {
          super.addAll(other);
          counter += other.size();
        } 
}
```
Die Idee hier war offensichtlich, dass die Klasse ```CountingList``` die Klasse ```ArrayList``` so erweitert, dass bei jedem Aufruf der Methode ```add``` ein Zähler inkrementiert wird. Leider funktioniert die Methode nicht, wie wir uns das vorstellen. Wenn wir ```addAll``` aufrufen, wird jedes Element zweimal gezählt. 
Der Grund dafür ist, dass in der Superklasse ```ArrayList```, die Methode ```addAll``` so implementiert ist, dass für jedes Element die Methode ```add``` aufgerufen wird. 
Somit zählen wir die Elemente zweimal. Wie die Methode ```addAll``` von der Superklasse implementiert wird, ist jedoch ein Implementationsdetail, dass uns in der Subklasse nicht interessieren dürfte. 

Eine bessere Lösung ist die folgende, die durch Komposition von Objekten erreicht wird.
```java
public class CountingList<T> implements List<T> {
        private final List<T> list = new ArrayList<T>();
        private int counter = 0;
       
        @Override
        public void add(T elem) {
          list.add(elem);
          counter++;
        }
       
        @Override
        public void addAll(Collection<T> other) {
          list.addAll(other);
          counter += other.size();
        } 
}
```
Diese Lösung implementiert das Relevante Interface ```List``` (Schnittstellenvererbung) und
nutzt die Implementation von ArrayList dadurch, dass es intern ein Objekt vom 
Typ ```ArrayList``` führt und alle Methodenaufrufe vom ```List``` Interface an dieses
Objekt delegiert. Die Kapselung ist intakt, Codeduplikation ist vermieden und die Verantwortlichkeiten sauber getrennt: ```CountingList``` kümmert sich um das Zählen, und 
```ArrayList``` um das Speichern der Elemente. Dieses Design ist dem vorigen eindeutig zu bevorzugen. 

Als Grundregel können wir uns merken, dass wir Komposition von Objekten immer der Implementationsvererbung bevorzugen sollten. 
