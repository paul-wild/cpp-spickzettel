# C++-Spickzettel für Programmierwettbewerbe

## 1) Listen und Arrays

* Dynamisches Array:

  https://en.cppreference.com/w/cpp/container/vector

```
      vector<vector<int>> dp(n,vector<int>(k,oo));

      vector<int> v;
      v.assign(n,-1);

      vector<pair<int,int>> a;
      while (cin >> x >> y) a.emplace_back(x,y);
```

  Wichtige Funktionen: `push_back()`, `operator[]`, `assign()`

  Kann im Gegensatz zu `deque` kein `push_front()`, hat aber deutlich bessere
  Laufzeitkonstanten, da als Array implementiert.

* Verkettete Listen:

    * https://en.cppreference.com/w/cpp/container/list

    * https://en.cppreference.com/w/cpp/container/stack

    * https://en.cppreference.com/w/cpp/container/queue

    * https://en.cppreference.com/w/cpp/container/deque

    Funktionen von `deque`: `front()`, `back()`, `push_back()`, `pop_front()`, ...

    Die anderen Datenstrukturen sind nur Wrapper um `deque`.

  ```
        deque<int> q = {7,1,3};
        q.push_front(4);
        q.push_back(5);

        cout << q[2] << endl;              // O(1) --> 1
        q.erase(begin(q)+2);               // O(n)
        for (int x: q) cout << x << endl;  // --> 4,7,3,5
  ```

* Bitset:

  https://en.cppreference.com/w/cpp/utility/bitset

  Wie `bool[]`, hat aber effiziente bitweise Operationen.

  Beispiel: transitive Hülle in einem Graphen via Floyd-Warshall,
  um Faktor 64 schneller:

```  
      vector<bitset<1000>> reach(n);
      for (auto e: edges) reach[e.xx][e.yy] = 1;

      FOR(i,0,n) reach[i][i] = 1;

      // slow:
      FOR(k,0,n) FOR(i,0,n) FOR(j,0,n)
        if (reach[i][k] && reach[k][j]) reach[i][j] = true;

      // fast:
      FOR(k,0,n) FOR(i,0,n) if (reach[i][k]) reach[i] |= reach[k];
```

## 2) Priority queue

https://en.cppreference.com/w/cpp/container/priority_queue

```
      priority_queue<int> q1;
      q1.insert(2), q1.insert(3);
      cout << q1.top() << endl;       // get maximum --> 3
      q1.pop();                       // remove maximum

      priority_queue<int, vector<int>, greater<int>> q2;
      q2.insert(2), q2.insert(3);
      cout << q2.top() << endl;       // get minimum --> 2
      q2.pop();                       // remove minimum
```

## 3) Produkt-Datentypen

* https://en.cppreference.com/w/cpp/utility/pair

```
      vector<pair<char,int>> pairs = {{'b',2},{'b',1},{'a',3},{'c',2}};

      sort(all(pairs));               // (a,3), (b,1), (b,2), (c,2)

      char x; int y;
      tie(x,y) = pairs[2];            // x = 'b', y = 2
```

* https://en.cppreference.com/w/cpp/utility/tuple

```
      tuple<int,char,string> tpl = make_tuple(42,'x',"foo");

      cout << get<1>(tpl) << endl;       // 'x'
```

* https://en.cppreference.com/w/cpp/container/array

```
      set<array<int,4>> states;
      states.insert({0,1,2,3});
```

## 4) Binäre Suchbäume

* https://en.cppreference.com/w/cpp/container/set

```
      set<int> s = {1,2,3};
      s.insert(2);
      s.erase(2);
      for (int x: s) cout << x << endl;     // --> 1,3

      cout << *s.lower_bound(1) << endl;    // --> 1
      cout << *s.upper_bound(1) << endl;    // --> 3

      if (s.count(5)) {
          cout << "5 is in s" << endl;
      } else {
          cout << "5 is not in s" << endl;
      }

      s.lower_bound(1);                     // O(log n)
      lower_bound(all(s),1);                // O(n)
```

* https://en.cppreference.com/w/cpp/container/map

Frequency Table mit `map`:

```
      map<string,int> cnt;
      string s;
      while (cin >> s) cnt[s]++; // operator[] adds elements if needed

      for (auto pr: cnt) {
          cout << pr.xx << " occurs " << pr.yy << " times" << endl;
      }
```

Wichtige Funktionen: `count()`, `erase()`, `insert()`, `find()`, `lower_bound()`


* https://en.cppreference.com/w/cpp/container/multiset

```
    multiset<int> s = {1,2,2,2,3};

    // erase single occurrence of 2:
    s.erase(s.find(2));

    // erase all occurrences of 2:
    s.erase(2);
```

* Order statistics tree:

```
      #include <bits/extc++.h>
      using namespace __gnu_pbds;

      // change null_type to something else to get a map
      template<typename T>
      using ordered_set = tree<T, null_type, less<T>, rb_tree_tag, tree_order_statistics_node_update>;

      ordered_set<int> s = {1,2,3,5,6,7};
      cout << s.order_of_key(4) << endl;       // O(log n) --> 3
      cout << s.find_by_order(5) << endl;      // O(log n) --> 7
```

## 5) Hash set/map

* https://en.cppreference.com/w/cpp/container/unordered_set

* https://en.cppreference.com/w/cpp/container/unordered_map

Wichtige Funktionen: wie bei `set` bzw. `map`

Operationen sind zwar O(1) statt O(log n), aber mit hohem konstanten Faktor.

## 6) Nützliche Funktionen aus `algorithm`

* Absteigend sortieren:

```    
      sort(all(v),greater<int>());
```

* Sortieren mit Lambda:

```      
      sort(all(v),[&](int x, int y) {
          if (x%2 != y%2) return x%2 < y%2;
          return x < y;
      });
```

* Duplikate entfernen:

```    
      sort(all(v));
      v.erase(unique(all(v)),end(v));
```

* https://en.cppreference.com/w/cpp/algorithm/swap

```
      string a  = "abc", b = "def";
      swap(a,b);
```

* https://en.cppreference.com/w/cpp/algorithm/is_sorted

```
      vector<int> a = {3,1,4,1,5};
      cout << is_sorted(all(a)) << endl;      // --> 0
```

* https://en.cppreference.com/w/cpp/algorithm/accumulate

```      
      ll sum_v = accumulate(all(v),0LL); // careful with overflow!
      ll product_v = accumulate(all(v),1LL,multiplies<ll>());
```

* https://en.cppreference.com/w/cpp/algorithm/max_element

```      
      ll max_v = *max_element(all(v));
```

* https://en.cppreference.com/w/cpp/algorithm/rotate

```      
      rotate(begin(v),begin(v)+k,end(v)); // rotate v so that the element at index k moves to the front
```

* https://en.cppreference.com/w/cpp/algorithm/copy

```
      vector<int> a = {1,2,3}, b = {4,5,6};
      copy(all(b),back_inserter(a));         // append all of b to a
```

* https://en.cppreference.com/w/cpp/algorithm/iota

* https://en.cppreference.com/w/cpp/algorithm/next_permutation

  Über alle Permutationen iterieren:

```      
      vector<int> v(n);
      iota(all(v),0);
      do {
          // do something
      } while (next_permutation(all(v)));
```

## 7) Iteratoren

* Binäre Suche auf sortierter Range:

  https://en.cppreference.com/w/cpp/algorithm/lower_bound

  https://en.cppreference.com/w/cpp/algorithm/upper_bound

  Achtung: auf `set`/`map` immer `s.lower_bound(x)` statt `lower_bound(all(s),x)` verwenden!

```
      vector<int> a = {1,3,5,5,6,10};
      cout << lower_bound(all(a),5) - begin(a) << endl; // --> 2
      cout << upper_bound(all(a),5) - begin(a) << endl; // --> 4
```

* In unsortierter Range suchen:

  https://en.cppreference.com/w/cpp/algorithm/find

```
      vector<int> a = {5,2,4,3,1};
      cout << find(all(a),4) - begin(a) << endl;       // --> 2
```

* Vorheriger/nächster Iterator:

  https://en.cppreference.com/w/cpp/iterator/prev

  https://en.cppreference.com/w/cpp/iterator/next

```      
      set<int> s = {3,11,19};
      auto it = s.upper_bound(x);
      if (it != end(s) && it != begin(s)) cout << *it - *prev(it) << endl;
      else cout << "unbounded" << endl;
```

* Rückwärts-Iteratoren:

  https://en.cppreference.com/w/cpp/iterator/rbegin

  https://en.cppreference.com/w/cpp/iterator/rend

```
      vector<int> a = {1,2,3,4};
      for (auto it = a.rbegin(); it != a.rend(); it++) {
          cout << *it << endl;                              // --> 4,3,2,1
      }
```

## 8) Zufallszahlen

* Zufallszahlengenerator:

```
      random_device rd;
      mt19937_64 gen1(42);         // fixed seed, same values on every run
      mt19937_64 gen2(rd());       // different seed on every run
```

* Zufallsverteilungen:

```
      uniform_int_distribution<ll> dis(a,b);     // both bounds inclusive
      FOR(i,0,10) cout << dis(gen1) << endl;
```

## 9) Bit-Hacks

```
      ll mask = (1LL << 10) - 1;

      // Population count = number of 1 bits
      cout << __builtin_popcountll(mask) << endl;    // --> 10

      // Number of leading zeros:
      cout << __builtin_clzll(mask) << endl;         // --> 54
```

Zweier-Logarithmus (abgerundet):

```    
      ll ld(ll x) { return 63 - __builtin_clzll(x); }
```

## 10) Höhere Rechengenauigkeit

* 128-Bit-Ganzzahlen: `__int128`

* 80-Bit-Gleitkommazahlen (64-Bit-Mantisse): `long double`


## 11) Mathe

* https://en.cppreference.com/w/cpp/numeric/math/round (`llround`)

* https://en.cppreference.com/w/cpp/numeric/math/hypot

```
      cout << hypot(3,4) << endl;    // --> 5
```

* Winkel zwischen (x,y) und positiver x-Achse:

  https://en.cppreference.com/w/cpp/numeric/math/atan2

```
      cout << atan2(y,x) << endl;
```

* Potenzen von Ganzzahlen:

```    
      #include <bits/extc++.h>
      using namespace __gnu_cxx;

      cout << pow(7,20) << endl;         // --> 7.97923e+16
      cout << power(7,20) << endl;       // --> -1199696159
      cout << power(7LL,20) << endl;     // --> 79792266297612001
```

* Größter gemeinsamer Teiler:

```    
      cout << __gcd(4,6) << endl; // 2
```

## 12) I/O

* Zahl mit gewünschter Anzahl Nachkommastellen ausgeben:

```      
      cout << fixed << setprecision(15) << M_PI << endl;   // --> 3.141592653589793
```

* Schnelleres I/O:

```      
      ios_base::sync_with_stdio(false);
      cin.tie(NULL);
```

* https://en.cppreference.com/w/cpp/string/basic_string/to_string

* https://en.cppreference.com/w/cpp/string/basic_string/stol

```
      string a = "1234";
      ll b = stol(a);
      string c = to_string(b);
```

* https://en.cppreference.com/w/cpp/string/basic_string/getline

* https://en.cppreference.com/w/cpp/io/basic_stringstream

  Folge von Zahlen aus Zeile einlesen:

```      
      string line;
      getline(cin,line);
      stringstream ss(line);
      vector<ll> v;
      ll x;
      while (ss >> x) v.push_back(x);
```

## 13) Sonstiges

* Arrays füllen:

```
      ll dp1[100][100][100][2];
      memset(dp1, 0x3f, sizeof dp1); // fill dp1 with oo

      ll dp2[100];
      fill_n(dp2, 50, 42); // fill half of dp2 with 42

      vector<pll> v;
      v.assign(100, {-1,-1}); // change size and fill
```
