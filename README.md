## EX. NO:2 IMPLEMENTATION OF PLAYFAIR CIPHER

 

## AIM:
 
To write a C program to implement the Playfair Substitution technique.

## DESCRIPTION:

The Playfair cipher starts with creating a key table. The key table is a 5×5 grid of letters that will act as the key for encrypting your plaintext. Each of the 25 letters must be unique and one letter of the alphabet is omitted from the table (as there are 25 spots and 26 letters in the alphabet).

To encrypt a message, one would break the message into digrams (groups of 2 letters) such that, for example, "HelloWorld" becomes "HE LL OW OR LD", and map them out on the key table. The two letters of the diagram are considered as the opposite corners of a rectangle in the key table. Note the relative position of the corners of this rectangle. Then apply the following 4 rules, in order, to each pair of letters in the plaintext:
1.	If both letters are the same (or only one letter is left), add an "X" after the first letter
2.	If the letters appear on the same row of your table, replace them with the letters to their immediate right respectively
3.	If the letters appear on the same column of your table, replace them with the letters immediately below respectively
4.	If the letters are not on the same row or column, replace them with the letters on the same row respectively but at the other pair of corners of the rectangle defined by the original pair.
## EXAMPLE:
![image](https://github.com/Hemamanigandan/EX-NO-2-/assets/149653568/e6858d4f-b122-42ba-acdb-db18ec2e9675)

 

## ALGORITHM:

STEP-1: Read the plain text from the user.
STEP-2: Read the keyword from the user.
STEP-3: Arrange the keyword without duplicates in a 5*5 matrix in the row order and fill the remaining cells with missed out letters in alphabetical order. Note that ‘i’ and ‘j’ takes the same cell.
STEP-4: Group the plain text in pairs and match the corresponding corner letters by forming a rectangular grid.
STEP-5: Display the obtained cipher text.




## Program:
```
#include <stdio.h> 
#include <string.h> 
#include <ctype.h> 
#define SIZE 5 
char keyMatrix[SIZE][SIZE]; 
// function to check if character already exists in table 
int existsInMatrix(char c, char table[SIZE][SIZE]) { 
for (int i = 0; i < SIZE; i++) 
for (int j = 0; j < SIZE; j++) 
if (table[i][j] == c) return 1; 
return 0; 
} 
// build key matrix (here using a numeric key just as shift offset) 
void generateKeyMatrix(int key) { 
char alphabet[26] = "ABCDEFGHIKLMNOPQRSTUVWXYZ"; // J merged with I 
int index = key % 25; // simple shift based on key value 
int k = 0; 
for (int i = 0; i < SIZE; i++) { 
for (int j = 0; j < SIZE; j++) { 
            while (existsInMatrix(alphabet[index], keyMatrix)) { 
                index = (index + 1) % 25; 
            } 
            keyMatrix[i][j] = alphabet[index]; 
            index = (index + 1) % 25; 
        } 
    } 
} 
 
// prepare plaintext for encryption 
void prepareText(char *text, char *prepared) { 
    int len = strlen(text); 
    int k = 0; 
    for (int i = 0; i < len; i++) { 
        char c = toupper(text[i]); 
        if (c == 'J') c = 'I'; // merge J with I 
        if (isalpha(c)) { 
            prepared[k++] = c; 
        } 
    } 
    prepared[k] = '\0'; 
 
    // insert X for double letters 
    char final[200]; 
    int f = 0; 
    for (int i = 0; i < k; i++) { 
        final[f++] = prepared[i]; 
        if (i + 1 < k && prepared[i] == prepared[i + 1]) { 
            final[f++] = 'X'; 
        } 
    } 
    if (f % 2 != 0) final[f++] = 'X'; // padding 
    final[f] = '\0'; 
    strcpy(prepared, final); 
} 
 
// find position of char in matrix 
void findPosition(char c, int *row, int *col) { 
    for (int i = 0; i < SIZE; i++) 
        for (int j = 0; j < SIZE; j++) 
            if (keyMatrix[i][j] == c) { 
                *row = i; 
                *col = j; 
                return; 
            } 
} 
 
// encryption 
void encryptPair(char a, char b, char *out1, char *out2) { 
    int r1, c1, r2, c2; 
    findPosition(a, &r1, &c1); 
    findPosition(b, &r2, &c2); 
 
    if (r1 == r2) { 
        *out1 = keyMatrix[r1][(c1 + 1) % SIZE]; 
        *out2 = keyMatrix[r2][(c2 + 1) % SIZE]; 
    } else if (c1 == c2) { 
        *out1 = keyMatrix[(r1 + 1) % SIZE][c1]; 
        *out2 = keyMatrix[(r2 + 1) % SIZE][c2]; 
    } else { 
        *out1 = keyMatrix[r1][c2]; 
        *out2 = keyMatrix[r2][c1]; 
    } 
} 
 
// decryption 
void decryptPair(char a, char b, char *out1, char *out2) { 
    int r1, c1, r2, c2; 
    findPosition(a, &r1, &c1); 
    findPosition(b, &r2, &c2); 
 
    if (r1 == r2) { 
        *out1 = keyMatrix[r1][(c1 - 1 + SIZE) % SIZE]; 
        *out2 = keyMatrix[r2][(c2 - 1 + SIZE) % SIZE]; 
    } else if (c1 == c2) { 
        *out1 = keyMatrix[(r1 - 1 + SIZE) % SIZE][c1]; 
        *out2 = keyMatrix[(r2 - 1 + SIZE) % SIZE][c2]; 
    } else { 
        *out1 = keyMatrix[r1][c2]; 
        *out2 = keyMatrix[r2][c1]; 
    } 
} 
 
// remove padding from decrypted text 
void cleanupDecryption(char *text, char *clean) { 
    int len = strlen(text); 
    int k = 0; 
    for (int i = 0; i < len; i++) { 
        if (i > 0 && text[i] == 'X' && text[i - 1] == text[i + 1]) { 
            continue; // remove filler X 
        } 
        clean[k++] = text[i]; 
    } 
    if (clean[k - 1] == 'X') k--; // remove trailing X 
    clean[k] = '\0'; 
} 
 
int main() { 
    char plaintext[100], prepared[200], encrypted[200], decrypted[200], cleaned[200]; 
    int key; 
 
    printf("Enter the key (number): "); 
    scanf("%d", &key); 
    printf("Enter text to encrypt: "); 
    scanf("%s", plaintext); 
 
    generateKeyMatrix(key); 
 
    printf("\nKey Matrix:\n"); 
    for (int i = 0; i < SIZE; i++) { 
        for (int j = 0; j < SIZE; j++) { 
            printf("%c ", keyMatrix[i][j]); 
        } 
        printf("\n"); 
    } 
 
    prepareText(plaintext, prepared); 
 
    // Encrypt 
    int len = strlen(prepared), k = 0; 
    for (int i = 0; i < len; i += 2) { 
        char c1, c2; 
        encryptPair(prepared[i], prepared[i + 1], &c1, &c2); 
        encrypted[k++] = c1; 
        encrypted[k++] = c2; 
    } 
    encrypted[k] = '\0'; 
 
    // Decrypt 
    k = 0; 
    for (int i = 0; i < strlen(encrypted); i += 2) { 
        char c1, c2; 
        decryptPair(encrypted[i], encrypted[i + 1], &c1, &c2); 
        decrypted[k++] = c1; 
        decrypted[k++] = c2; 
    } 
    decrypted[k] = '\0'; 
 
    cleanupDecryption(decrypted, cleaned); 
 
printf("\nEncrypted Text: %s", encrypted); 
printf("\nDecrypted Text: %s\n", cleaned); 
return 0; 
}
```





## Output:

<img width="544" height="481" alt="image" src="https://github.com/user-attachments/assets/31ae8c7d-184a-4f16-b975-20ebe04a8ccc" />

## Result:
Thus the implementation of playfair cipher had been executed successfully.

