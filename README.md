
# Objektno orientirano programiranje

```java
// Prva moznost: Podatki loceni na pomnilniku
String[] imena = new String[];
int[] starosti = new int[];
imena[0] = "Jure";
starosti[0] = 23;

// Druga moznost: Objekt - vsi podatki so zapisani skupaj na pomnilniku
class Oseba {
    String ime;
    int starost;
}

new Oseba("Jure", 23);
```

## Primer - razlika med strukturo in objektom
Glej `primer1.py` v Pythonu.

```java
class Racun {
    String trgovina;
    int[] cene;
}
```

## Primer - račun v Javi
```java
class Racun {

    // PARAMETRI objekta Racun
    String trgovina;
    int[] cene;

    // KONSTRUKTOR OBJEKTA - je funkcija, ki se izvede le enkrat.
    // Ko kreiramo objekt. Objekt ustvarimo tako, da klicemo:
    // new Racun(...)
    // Konstruktor navadno inicializira vse parametre objekta.
    public Racun(String trgovina, int[] cene) {
        // Ce bi dali ime argumenta trgovina, bi se pojavil problem,
        // da bi imeli stavek naslednje oblike:
        // trgovina = trgovina;
        // Ker pa prevajalnik ni tako pameten, kot smo mi, ne ve kaj
        // storiti.

        // Ce zelimo prevajalniku povedati, da nek parameter
        // nastaviti na zeleno vrednost, lahko to nakazemo s kljucno
        // besedo this.
        this.trgovina = trgovina;
        this.cene = cene;
    }

    // METODA - je funkcija, ki jo kličemo nad objektom.
    // Pomembno - metoda ni nikoli staticna.
    public int skupnaCena() {
        // Opomba: Uporabili smo this.cene, ceprav se v tej metodi pojavi
        // le ena spremenljivka z imenom cene.
        // Za ostale programerje, ki berejo tvojo kodo, je mogoce smiselno
        // vedno uporabljati this.parameter za dostop do polj tega objekta.
        int vsota = 0;
        for (int i = 0; i < this.cene.length; i++) {
            vsota += this.cene[i];
        }
        return vsota;
    }

}

public static void main(String[] args) {
    int[] cene = { 5, 8, 13, 52 };
    Racun racunMercator = new Racun("Mercator", cene);
    int skupnaCena = racunMercator.skupnaCena();
    System.out.println(skupnaCena);
}
```

## Razlika med statičnimi funkcijami in metodami
```java
class Meth {

    double haha() {
        return 2.0;
    }

    static double random() {
        return 0.25;
    }

}

public static void main(String[] args) {
    Meth meth = new Meth();
    // haha je metoda, ki jo lahko klicemo le, ce imamo ustvarjen objekt.
    // Nad instanco (= kvadratkom na pomnilniku) izvedi metodo haha.
    meth.haha();

    // Funkcija random je staticna funkcija, za katero NI potrebno imeti
    // ustvarjenega objekta!
    // int prvaNaloga = (int) (Meth.random() * 49 + 1);
    // Pojdi v RAZRED Meth in izvedi metodo random.
    Meth.random();
    
    // To NE BO DELALO, ker haha ni staticna metoda in jo lahko klicemo SAMO
    // v primeru, da imamo ustvarjeno instanco objekta Meth.
    Meth.haha();
}
```

## Statična polja (parametri)
```java
class Ovca {

    // Spremenljvika je staticna, kar pomeni, da je loceno na pomnilniku
    // od vseh ostalih kvadratkov za objekte.
    // Ta spremenljiva je vedno LE ENA. Do nje dostopamo z ImeRazreda.imeSpremenljivke
    static int steviloOvac = 0;

    // Parameter objekta. Obstaja v VSAKEM objektu.
    String ime;

    public Ovca(String ime) {
        this.ime = ime;

        // Poveca se staticna spremenljvika. Ta je VEDNO le ena. Vsakic ko se
        // ustvari nov objekt, se staticna spremenljivka poveca.
        Ovca.steviloOvac += 1;
    }

}

public static void main(String[] args) {
    Ovca marija = new Ovca("Marija");
    Ovca tanja = new Ovca("Tanja");
    Ovca mateja = new Ovca("Mateja");
    System.out.println(Ovca.steviloOvac); // Izpise 3
}
```

## Vidnost objekta
```java

class Oseba {

    // Do imena osebe lahko dostopamo SAMO znotraj tega objekta IN objektov
    // istega tipa.
    private String ime;
    private String priimek;

    // Do tega parametera lahko dostopamo od kjerkoli. Tudi SPREMINJAMO ga
    // lahko kjerkoli.
    public Oseba najboljsiPrijatelj = null;

    public Oseba(String ime, String priimek) {
        this.ime = ime;
        this.priimek = priimek;
    }

    void nastaviNajboljsegaPrijatelja(Oseba najboljsiPrijatelj) {
        // Tukaj lahko dostopava do najboljsiPrijatelj.ime, ceprav je private.
        // TODO: Preveri doma ali to res drzi.

        // Nastaviva povezavo oseba -> prijatelj
        this.najboljsiPrijatelj = najboljsiPrijatelj;
        // Nastaviva povezavo prijatelj -> oseba
        najboljsiPrijatelj.najboljsiPrijatelj = this;

        // Ne smemo tako narediti! Infinite loop.
        // najboljsiPrijatelj.nastaviNajboljsegaPrijatelja(this);
    }

    // GETTER - samo vrne vrednost. Mora biti public oziroma protected, 
    // ker sicer nima smisla. Ce je parameter ze private, zakaj bi potem
    // imeli se getter privaten?
    public String getIme() {
        return this.ime + " " + this.priimek;
    }

    // SETTER - nastavimo vrednost polja ime. Naj bo vedno public.
    public void setIme(String ime) {
        String[] podatki = ime.split(" ");
        this.ime = podatki[0];
        this.priimek = podatki[1];
    }

}

public static void main(String[] args) {
    Oseba tone = new Oseba("Tone", "Mavrič");
    Oseba peter = new Oseba("Peter", "Novak");
    tone.nastaviNajboljsegaPrijatelja(peter);

    // Lahko dostopamo in spreminjamo peter.najboljsiPrijatelj
    // Ne smemo dostopati in spreminjati peter.ime

    // Mi kot uporabnik lahko NE vidimo kaj se natancno dogaja
    // znotraj nekega objekta.
    // Vidimo pa podpise public metod (ime in parametri)
    // Pri Oseba vidimo:
    //   * oseba.najboljsiPrijatelj
    //   * oseba.nastaviNajboljsegaPrijatelja()
    //   * oseba.getIme()
    //   * oseba.setIme(String ime)
    // Mi kot uporabnik lahko klicemo te metode, ni pa nujno da
    // vemo kaj natancno delajo v ozadju.
}

```

## Dedovanje

```java
class Avto {

    String znamka;
    String barva;

    public Avto(String znamka, String barva) {
        this.znamka = znamka;
        this.barva = barva;
    }

    // Ta funkcija se PODEDUJE tudi na otroka. To pomeni, da lahko to
    // funkcijo klicemo na objektih tipa Mazda
    // Mazda mazda = new Mazda("rumena", 5);
    // mazda.potrobi(); bo poklicala to metodo.
    public void potrobi() {
        System.out.println("HONK HONK");
    }

}

class Mazda extends Avto {

    // Ker deduje od Avto, ima vedno se dva parametra:
    //   * znamka
    //   * barva
    // Pri konstrukciji objekta Sedan, je potrebno poklicati
    // tudi konstruktor razreda Avto.
    
    // Dodaten parameter, ki ga ima LE Mazda.
    int steviloSedezev;

    public Mazda(String barva, int steviloSedezev) {
        // Klicali bomo konstruktor starsevskega objekta (tj. objekta Avto)
        // Iz starsa ne moremo nikoli klicati konstruktor otroka
        super("Mazda", barva);
        // Ta konstruktor bo nastavil parametra znamka in barva.
        // Ce zdaj dostopamo do this.znamka, bomo dobili vrednost "Mazda"

        // this.barva je ubistvu parameter barva od razreda Avto
        // this.barva = barva;
        this.steviloSedezev = steviloSedezev;
    }

    // PREPISANA METODA od Avto. Ce ustvarimo objekt tipa Mazda in nad
    // njim klicemo metodo potrobi, se bo izvedla ta metoda.
    // PREPISALI smo metodo potrobi od objekta avto.
    // Ce je ne bi, bi se ob klicu mazda.potrobi() izvedla metoda
    // potrobi od objekta Avto.
    public void potrobi() {
        System.out.println("HRMF HRMF");
    }

}
```

## Večobličnost (= polimorfizem)

```java
class Izpisovalec {
    
    public static void izpisi(String tekst) {
        System.out.println(tekst);
    }

    // Isti ime in return type metode, a drugacni parametri. Java zna
    // sama ugotoviti katera izmed metod se more izvesti.
    public static void izpisi(int stevilka) {
        Izpisovalec.izpisi("Stevilka: " + stevilka);
    }

}

public static void main(String[] args) {
    Izpisovalec.izpisi("Test"); // izpise Test
    Izpisovalec.izpisi(133); // izpise Stevilka: 133
}
```

## Primer dedovanja
```java
import java.util.*;

public static void main(String[] args) {
    
    ArrayList<String> imena = new ArrayList<String>();
    LinkedList<String> priimki = new LinkedList<String>();

    // ArrayList in LinkedList dedujeta od List
    // to pa pomeni, da imata oba iste metode kot jih ima List.
}
```
