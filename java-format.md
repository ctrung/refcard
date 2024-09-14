## String.format()

```java
var pi = 3.14159265359;
System.out.format("[%f]",pi);       // [3.141593]
System.out.format("[%12.8f]",pi);   // [  3.14159265]
System.out.format("[%012f]",pi);    // [00003.141593]
System.out.format("[%12.2f]",pi);   // [        3.14]
System.out.format("[%-12.2f]",pi);  // [3.14        ]
System.out.format("[%.3f]",pi);     // [3.142]
```

Caractères spéciaux de formatage :
- %`x`  : longueur totale + padding gauche avec des espaces
- %`-x` : longueur totale + padding droit avec des espaces
- %`0x` : longueur totale + padding avec des zéros
- .`x`f : nombre de chiffre après la virgule

## NumberFormat

`interface NumberFormat` \
`class DecimalFormat implements NumberFormat`

```java
// locale fr, FR

double d = 1234.567;
NumberFormat f1 = new DecimalFormat("###,###,###.0");
System.out.println(f1.format(d)); // 1 234.6

NumberFormat f2 = new DecimalFormat("000,000,000.00000");
System.out.println(f2.format(d)); // 000 001 234.56700

NumberFormat f3 = new DecimalFormat("Votre solde €#,###,###.##");
System.out.println(f3.format(d)); // Votre solde €1 234.57
```

Caractères spéciaux de formatage :
- `#` : pas d'affichage si pas de chiffre à cette position
- `0` : afiichage d'un zéro si pas de chiffre à cette position
