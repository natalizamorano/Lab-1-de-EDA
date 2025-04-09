# Lab-1-de-EDA
import java.util.Scanner;

// Clase que contiene la lógica
class BigVigenere {
    int[] key; // Clave numérica
    char[][] alphabet; // Alfabeto extendido de 64x64

    public BigVigenere() {
        Scanner lector = new Scanner(System.in);  // lector puede variar, lo demas no
        System.out.println("Introduzca su clave: "); //printl con salto de linea, print sin salto

        String Lee_Key = lector.nextLine();
        // s.nextLine lee lo que el usuario escribio

        key = new int[Lee_Key.length()]; // length devuelve el número de caracteres

        for (int i = 0; i < Lee_Key.length(); i++) { // lee la llave y devuelve los caracteres que tiene, ese será el tope

            char c = Lee_Key.charAt(i);
            // A-Z (0-25)
            if (c >= 'A' && c <= 'Z') {
                key[i] = c - 'A';  // letras mayúsculas
            }
            // a-z (26-51)
            else if (c >= 'a' && c <= 'z') {
                key[i] = c - 'a' + 26;  // letras minúsculas
            }
            // 0-9 (52-61)
            else if (c >= '0' && c <= '9') {
                key[i] = c - '0' + 52;  // números
            }
            // ñ (62)
            else if (c == 'ñ') {
                key[i] = 62;  // letra ñ
            }
            // Ñ (63)
            else if (c == 'Ñ') {
                key[i] = 63;  // letra Ñ
            }
        }

        // genera la matriz del alfabeto con 64 caracteres
        alphabet = new char[64][64]; // crea matrix 64x64 donde se guardarán los caracteres
        for (int i = 0; i < 64; i++) {  // recorre cada fila de la matriz
            for (int j = 0; j < 64; j++) { // recorre cada columna de la fila para llenarla
                // llena la matriz con los caracteres
                if (i < 26) { // llena las primeras 26 filas con mayúsculas para que se repitan en orden con desplazamiento
                    alphabet[i][j] = (char) ('A' + (i + j) % 26);  // A-Z
                } else if (i < 52) {
                    alphabet[i][j] = (char) ('a' + (i + j - 26) % 26);  // a-z, de la fila 21 a 51 se llena con minúsculas
                } else if (i < 62) {
                    alphabet[i][j] = (char) ('0' + (i + j - 52) % 10);  // 0-9, de la fila 52 a la 61 se usan los números
                } else if (i == 62) {
                    alphabet[i][j] = 'ñ';  // fila 62 se llena con ñ
                } else {
                    alphabet[i][j] = 'Ñ';  // fila 63 se llena con Ñ
                }
            }
        }

        // mostrar la clave codificada como números
        System.out.print("Clave procesada: ");
        for (int k : key) {
            System.out.print(k + " ");
        }
        System.out.println();
    }

    private int[] convertKey(String keyStr) { // convierte la clave de string a una clave numérica para cifrar y desifrar los msj
        int[] numericKey = new int[keyStr.length()]; // crea un arreglo del tamaño de la llave para guardar los valores numerios de la clave
        for (int i = 0; i < keyStr.length(); i++) { // bucle con cada caracter de la clave
            char c = keyStr.charAt(i); ////obtiene el caracter de la posicion i
            if (c >= 'A' && c <= 'Z') numericKey[i] = c - 'A'; //  convierte las mayusculas en un valor numerico y se almacena en i
            else if (c >= 'a' && c <= 'z') numericKey[i] = c - 'a' + 26;// lo mismo pero en minusculas
            else if (c >= '0' && c <= '9') numericKey[i] = c - '0' + 52;// lo mismo en numeros
            else if (c == 'ñ') numericKey[i] = 62;// agregamos valor de ñ minuscula y en la siguiente de l mayuscula
            else if (c == 'Ñ') numericKey[i] = 63;
        }
        return numericKey; // devuelve los valores numericos correspondientes
    }

    // genera la matriz de caracteres del alfabeto
    private void generateAlphabet() {
        alphabet = new char[64][64]; // inicia la matriz
        for (int i = 0; i < 64; i++) { // bucle para recorrer las filas
            for (int j = 0; j < 64; j++) { // bucle para recorrer columnas
                if (i < 26) alphabet[i][j] = (char) ('A' + (i + j) % 26); //si se esta en las primeras 26 f, se asigna el caracter mayusculas con el desplazamiento i+.... para recprrer las letras A-Z
                else if (i < 52) alphabet[i][j] = (char) ('a' + (i + j - 26) % 26);//si se esta entre las filas 26 y 51. asigna las minusculas con un desplazamiento similar
                else if (i < 62) alphabet[i][j] = (char) ('0' + (i + j - 52) % 10);//entre la 52 y 61 asigna los numeros 0-9
                else if (i == 62) alphabet[i][j] = 'ñ';// asigna un valor a letra ñ y dsp la Ñ
                else alphabet[i][j] = 'Ñ';
            }
        }
    }

    // cifra el mensaje usando la clave numérica
    public String encrypt(String message) {
        StringBuilder encrypted = new StringBuilder(); //construye el msj cifrado de forma eficiente
        for (int i = 0; i < message.length(); i++) { // bucle que recorre cada caracter
            char c = message.charAt(i);// obtiene el caracter de la posicion i
            int pos = charToIndex(c); //convierte el caracter c en lo que corresponde con la matriz alphabet con la funcion chartoindex
            if (pos == -1) continue; // si el caracter no esta, se lo salta
            int keyPos = key[i % key.length];//obtiene el valor de la clave para la posicion i%..
            encrypted.append(alphabet[keyPos][pos]); //cifra el caracter y lo añade al msj cifrado utilizando el valor de la clave y la posicion del caracter
        }
        return encrypted.toString(); // devuelve el msj cifrado como cadena
    }

    // descifra el mensaje usando la clave numérica
    public String decrypt(String encryptedMessage) { //construye el msj descifrado de forma eficiente
        StringBuilder decrypted = new StringBuilder();
        for (int i = 0; i < encryptedMessage.length(); i++) { // bucle que recorre cada caracter del msj cifrado
            char c = encryptedMessage.charAt(i);//obtiene el caracter cifrado en la posicion i
            int keyPos = key[i % key.length];//obtiene el valor de la clave para la posicion i%..
            for (int j = 0; j < 64; j++) { // bucle que recorre las filas de la matriz alphabet
                if (alphabet[keyPos][j] == c) { //busca el caracter cifrado en la fila correspondiente de  la  matriz alphabet
                    decrypted.append(indexToChar(j)); // cunado encuentre el caracter en la matriz, lo convierte en el valor correspondiente
                    break; //sale del bucle cuando encuentra el caracter correspondiente
                }
            }
        }
        return decrypted.toString(); //devuelve el mensaje cifrado como una cadena
    }

    // cambia la clave para realizar un nuevo cifrado
    public void reEncrypt() {
        Scanner scanner = new Scanner(System.in);// crea un objeto para leer la entrada

        System.out.print("Ingrese el mensaje cifrado: "); //solicita al usuario que ingrese el mensaje cifrado
        String encryptedMessage = scanner.nextLine();// lee el msj del usuario

        String decryptedMessage = decrypt(encryptedMessage);// descifra el msj usando la clave actual
        System.out.println("Mensaje descifrado con la clave actual: " + decryptedMessage);// muestra el msj cifrado

        System.out.print("Ingrese la nueva clave numérica: ");//solicita al usuario nueva clave
        String newKey = scanner.nextLine();//lee la nueva clave
        this.key = convertKey(newKey);//convierte la clave en su formato numerico y actualiza la clave

        String newEncryptedMessage = encrypt(decryptedMessage);//cifra nuevamente el msj descifrado con la nueva clave
        System.out.println("Nuevo mensaje cifrado: " + newEncryptedMessage);//muestra el msj cifrado con la nueva clave
    }

    // busca el caracter correspondiente a la posición en la matriz
    public char search(int position) {  
        int count = 0;         // se inicializa contador count, representa la posicion actual al recorrer la matriz

        for (int i = 0; i < 64; i++) {
            for (int j = 0; j < 64; j++) { // se recorre la matriz alphabet de 64x64 usando dos bucles
                if (count == position) {
                    return alphabet[i][j];//si el contadoe llega al numero de posicion deseada, se retorna el caracter  en esa posicion
                }
                count++;
            }
        }
        return '?'; //si nunca encuentra la posicion, se devuelve ? por defecto
    }

    // busqueda optimizada de caracter correspondiente a la posicion en la matriz
    public char optimalSearch(int position) {
        int row = position / 64; // division entera del indice por 64
        int col = position % 64; // el resto (posicion dentro de la fila)
        return alphabet[row][col]; //devuelve el caracter directamente desde la matriz sin recorrerla
    }

    // convierte un caracter a su indice en la matriz
    private int charToIndex(char c) {
        if (c >= 'A' && c <= 'Z') return c - 'A'; //si es letra mayuscula, devuelve posicion entre 0-25
        else if (c >= 'a' && c <= 'z') return c - 'a' + 26;// si es minuscula, suma 26 para colocarla dsp de las mayusculas
        else if (c >= '0' && c <= '9') return c - '0' + 52;//si el numero es del 0-9 suma 52
        else if (c == 'ñ') return 62;
        else if (c == 'Ñ') return 63;//asigna  directamente los valores 62 y 63 a ñ y Ñ
        else return -1; // si no reconoce el caracter, retorna -1 como error
    }

    // convierte un indice a su caracter correspondiente
    private char indexToChar(int index) {
        if (index >= 0 && index < 26) return (char) ('A' + index);//si esta entre 0-25 se convierte denuevo en mayuscula
        else if (index < 52) return (char) ('a' + index - 26);//entre 26-51 lo convierte en minusculas
        else if (index < 62) return (char) ('0' + index - 52);// entre 52 y 61, lo convierte en 0-9
        else if (index == 62) return 'ñ';
        else return 'Ñ';// 62 y 63 de ñ y Ñ
    }
}

// clase que se ejecuta primero
public class Main {
    public static void main(String[] args) {
        BigVigenere bv = new BigVigenere();  // ejecuta el constructor y se crea una instancia de BigVigenere lo cual inicializa el cifrador
        Scanner sc = new Scanner(System.in);
        System.out.print("Ingrese mensaje a cifrar: ");
        String mensaje = sc.nextLine(); // se pide al usuario que ingrese un mensaje, que se guara en mensaje

        String cifrado = bv.encrypt(mensaje);
        System.out.println("Mensaje cifrado: " + cifrado);// se cifra el msj y se imprime en pantalla

        String descifrado = bv.decrypt(cifrado);
        System.out.println("Mensaje descifrado: " + descifrado);//se describe el msj para comprobar que todo funciona correctamente

        bv.reEncrypt(); // permite cambiar la clave
    }
}
