# Flutter

1.Pada halaman tersebut akan bertujuan membuat program unlimited scroll yang berisi nama nama untuk sebuah perusahaan startup. Dalam file lib/main.dart, semua kode awal bawaan       flutter di replace dengan kode baru
     import 'package:flutter/material.dart';

       void main() => runApp(MyApp());

      class MyApp extends StatelessWidget {
        @override
        Widget build(BuildContext context) {
          return MaterialApp(
            title: 'Welcome to Flutter',
            home: Scaffold(
              appBar: AppBar(
                title: const Text('Welcome to Flutter'),
              ),
              body: const Center(
                child: Text('Hello World'),
              ),
            ),
          );
        }
      }
   Program dijalankan dengan media chrome karena belum punya android studio, saat dijalankan akan menampilkan tulisan hello world, selanjutnya menambahkan nama nama baru dengan    mengimport paket/library "english_word" yang berisi banyak kata dalam bahasa inggris. Dalam file pubspec.yaml ditambahkan "english_words: ^4.0.0" agar paket/lib dapat            digunakan, selanjutnya diimport paket/lib tersebut dalam kode "import 'package:english_words/english_words.dart';". Ditambahkan fungsi "final wordPair = WordPair.random();"      untuk memanggil kata random, dalam kode body tulisan hello world digantikan "child: Text(wordPair.asPascalCase)," sebagai tempat text random muncul. Membuat stateful widget 
   
    class RandomWords extends StatefulWidget {
      @override
      _RandomWordsState createState() => _RandomWordsState();
    }

    class _RandomWordsState extends State<RandomWords> {
      @override
      Widget build(BuildContext context) {
        final wordPair = WordPair.random();
        return Text(wordPair.asPascalCase);
      }
    }
   Dalam kode sebelumnya ditambahkan "child: RandomWords()," dibawah baris "child: Text(wordPair.asPascalCase),". Aplikasi kemudian di reload/hot reload yang nantinya akan          memunculkan kata baru setiap di reload. Membuat infinite scroll tambahkan kode ini dalam "class _RandomWordsState extends State<RandomWords>"
	class _RandomWordsState extends State<RandomWords> {
		final _suggestions = <WordPair>[];
		final _biggerFont = const TextStyle(fontSize: 18.0);
	    // ···
	  }
  _suggestion berfungsi menyimpan sugesti kata yang sudah muncul, sedangkan _biggerfont membuat text lebih besar. Tambahkan _buildSuggestion
Widget _buildSuggestions() {
  return ListView.builder(
      padding: const EdgeInsets.all(16.0),
      itemBuilder: /*1*/ (context, i) {
        if (i.isOdd) return const Divider(); /*2*/

        final index = i ~/ 2; /*3*/
        if (index >= _suggestions.length) {
          _suggestions.addAll(generateWordPairs().take(10)); /*4*/
        }
        return _buildRow(_suggestions[index]);
      });
}
	
  berfungsi untuk memanggil kembali kata yang sudah muncul, dalam fungsi ini ada _buildRow berfungsi menampilkan pasangan kata baru agar lebih menarik.
	
Widget _buildRow(WordPair pair) {
    return ListTile(
      title: Text(
        pair.asPascalCase,
        style: _biggerFont,
      ),
    );
  }
    @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Startup Name Generator'),
      ),
      body: _buildSuggestions(),
    );
  }
  Kemudian ganti nama app yang sudah dibuat menjadi startup name generator 
  class MyApp extends StatelessWidget {
	    @override
    Widget build(BuildContext context) {
	      return MaterialApp(
       title: 'Welcome to Flutter',
       home: Scaffold(
         appBar: AppBar(
           title: const Text('Welcome to Flutter'),
         ),
         body: Center(
           child: RandomWords(),
         ),
       ),
       title: 'Startup Name Generator',
       home: RandomWords(),
	      );
	    }
	
2. Lanjutan dari program sebelumnya, menambahkan icon pada setiap nama random yang muncul, menambahkan class _saved sebagai tempat menampung nama favorit
	
class _RandomWordsState extends State<RandomWords> {
	final _suggestions = <WordPair>[];
	final _saved = <WordPair>{};     // NEW
	final _biggerFont = TextStyle(fontSize: 18.0);
	 ...
}
   Menambahkan class alreadySaved agar memastikan nama yang sudah favorit tidak dapat difavoritkan lagi
	Widget _buildRow(WordPair pair) {
	  final alreadySaved = _saved.contains(pair);  // NEW
	  ...
	}
   Menambahkan listTitle berisi bentuk icon hati sebagai tanda favorit
	Widget _buildRow(WordPair pair) {
	  final alreadySaved = _saved.contains(pair);
	  return ListTile(
	    title: Text(
	      pair.asPascalCase,
	      style: _biggerFont,
	    ),
	    trailing: Icon(   // NEW from here... 
	      alreadySaved ? Icons.favorite : Icons.favorite_border,
	      color: alreadySaved ? Colors.red : null,
	      semanticLabel: alreadySaved ? 'Remove from saved' : 'Save',
	    ),                // ... to here.
	  );
	}
	
  Jika app di reload maka akan dapat dilihat bedanya sekarang sudah ada icon hati
  Membuat icon hati dapat berinteraksi ketika disentuh 
	
	Widget _buildRow(WordPair pair) {
	  final alreadySaved = _saved.contains(pair);
	  return ListTile(
	    title: Text(
	      pair.asPascalCase,
	      style: _biggerFont,
	    ),
	    trailing: Icon(
	      alreadySaved ? Icons.favorite : Icons.favorite_border,
	      color: alreadySaved ? Colors.red : null,
	      semanticLabel: alreadySaved ? 'Remove from saved' : 'Save',
	    ),
	    onTap: () {      // NEW lines from here...
	      setState(() {
		if (alreadySaved) {
		  _saved.remove(pair);
		} else { 
		  _saved.add(pair); 
		} 
	      });
	    },               // ... to here.
	  );
	}
	
  Sekarang jika disentuh maka icon hati akan berubah warna merah tanda item sudah difavoritkan.
  Membuat aplikasi dapat berpindah dengan "navigator" dengan kode dibwh ini
	
	class _RandomWordsState extends State<RandomWords> {
	  ...
	  @override
	  Widget build(BuildContext context) {
	    return Scaffold(
	      appBar: AppBar(
		title: Text('Startup Name Generator'),
		actions: [
		  IconButton(
		    icon: const Icon(Icons.list),
		    onPressed: _pushSaved,
		    tooltip: 'Saved Suggestions',
		  ),
		],
	      ),
	      body: _buildSuggestions(),
	    );
	  }
	  ...
	}
  Menambahakan list favorit sebagai tempat item item favorit
	void _pushSaved() {
  }
	void _pushSaved() {
	  Navigator.of(context).push(
	  );
	}
 Reload aplikasi maka sekarang sudah bisa dilihat item favoritnya
	void _pushSaved() {
    Navigator.of(context).push(
      // Add lines from here...
      MaterialPageRoute<void>(
        builder: (context) {
          final tiles = _saved.map(
            (pair) {
              return ListTile(
                title: Text(
                  pair.asPascalCase,
                  style: _biggerFont,
                ),
              );
            },
          );
          final divided = tiles.isNotEmpty
              ? ListTile.divideTiles(
                  context: context,
                  tiles: tiles,
                ).toList()
              : <Widget>[];

          return Scaffold(
            appBar: AppBar(
              title: const Text('Saved Suggestions'),
            ),
            body: ListView(children: divided),
          );
        },
      ), // ...to here.
    );
  }

 Membuat tema untuk aplikasi
	
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Startup Name Generator',
      theme: ThemeData(          // Add the 5 lines from here... 
        appBarTheme: const AppBarTheme(
          backgroundColor: Colors.white,
          foregroundColor: Colors.black,
        ),
      ),                         // ... to here.
      home: RandomWords(),
    );
  }
}
	
Reload maka aplikasi sudah dapat diganti temanya
