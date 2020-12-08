# CTF_Write-ups

![STF_banner](https://user-images.githubusercontent.com/15940750/101498423-0b7d1300-39a7-11eb-8258-6e0c48625570.png)
## Corrupted Hive
---
### Category: Forensics
![1](https://user-images.githubusercontent.com/15940750/101498399-061fc880-39a7-11eb-8158-b622cfcd4368.png)

In this challenge, we are presented with a (.hive) file which is in the Windows registry hive file format. In order to parse this hive file, we will use the tool **RegRipper** which is a forensics tool used to extract information from the Windows registry hive files via perl scripts.

![2](https://user-images.githubusercontent.com/15940750/101498405-0750f580-39a7-11eb-936c-433e66f93e10.png)

In the **RegRipper** GUI, we just need to supply the hive file and the directory of the output report file. 

![3](https://user-images.githubusercontent.com/15940750/101498408-07e98c00-39a7-11eb-976c-c094e269454f.png)

Unfortunately, there is an error in parsing the hive file and it seems like nothing can be extracted by using **RegRipper**. Since the challenge name is ‘Corrupted Hive’, we can expect that parsing the hive file using an automated tool will not yield much results. Hence, we proceeded to parsing the hive file manually by using a hex editor.
As mentioned in the description, we have a clue from Forensic-Challenge-2, which is a pre-requisite to unlocking Forensic-Challenge-3. 

![4](https://user-images.githubusercontent.com/15940750/101498409-07e98c00-39a7-11eb-8f27-50fcd1c935dd.png)

Inside the hex editor (we will be using **HxD** editor here), we tried to search for the “covid”.

![5](https://user-images.githubusercontent.com/15940750/101498411-08822280-39a7-11eb-96d9-dcfc621ccf71.png)

Once again, there seems to be nothing really useful here. The string : “Value ( Kcnn{S3tlfnyr}” seems like valuable information but we have no idea how to make use of it.
We then proceeded to try and use Sysinternals’ **Strings.exe** to see if we can get any other useful information. We will be using the switch `-n 5` here to specify the minimum string length because there seems to be a lot of strings inside this file. Fun fact: By default, **Strings.exe** uses a minimum string length of 3. Also, it is worth noting that we could have used any other number above 5 to increase the filtering but we would risk missing important strings.

![6](https://user-images.githubusercontent.com/15940750/101498413-091ab900-39a7-11eb-8535-f6ab77484b52.png)

After running **Strings.exe**, it seems that we have found something very interesting. Near the “important” strings which immediately caught our attention, there is a string that look like a base64 encoded string. Fun fact: In Base64 encoding, if the output string is not a multiple of 3, it will be padded with the ‘=’ character at the end.

![7](https://user-images.githubusercontent.com/15940750/101498414-091ab900-39a7-11eb-93be-d9040e1a7f31.png)

Using **CyberChef**, (or any other base64 decoder), we got the decoded output string:
> gvnlqop-sot{W3n157uu_U1a3} 

The format of the string appears to resemble the flag a lot but it seems encrypted/encoded. So, we created a mapping from the encrypted string to the expected string.

![8](https://user-images.githubusercontent.com/15940750/101498415-09b34f80-39a7-11eb-8378-a497d5fec0e2.png)

Our first instinct is that it might be a Caesar cipher but seeing how ‘g’ was mapped to ‘g’ it cannot be a Caesar cipher. 
Our next guess is affine cipher because affine cipher has the property of having the same character mapped to the exact same character. However, ‘o’ was mapped to 2 different characters. It is first mapped from ‘o’ to ‘c’ but then later mapped from ‘o’ to ‘s’. Which leads us to conclude that it cannot be a monoalphabetic cipher. 
Our next guess is that it might be a keystream cipher but taking a look at the decoded string again, it seems very improbable too. What are the chances that all characters in the front part of the decoded string are lower-cased non-alphanumeric character and the rear part has a mixed of both upper and lower-cased alphanumeric character, if a keystream was used?
It is also worth noticing that only the numbers 1, 3, 5 and 7 appeared which is tantamount to screaming LEET! (or “1337” if you may)

![9](https://user-images.githubusercontent.com/15940750/101498417-0a4be600-39a7-11eb-8815-dc86cfa94fa7.png)

Here we quickly created a mapping of leet translation and modified the decoded string a bit to:
> gvnlqop-sot{?e?ist??_?i?e} 

Ignoring the front portion of the decoded string, we fed this to a hangman solver, 1 word at a time.

![10](https://user-images.githubusercontent.com/15940750/101498419-0a4be600-39a7-11eb-999f-2b67436b3cbd.png)

The solver returned 2 words and it is immediately obvious which word it was.

![11](https://user-images.githubusercontent.com/15940750/101498421-0ae47c80-39a7-11eb-8ea8-1e7b53221d54.png)

The hangman solver returned many results for the second word but given the first word and the name of this challenge itself, it is not difficult to tell what is the flag already.
Finally, this is the flag after we manually convert it back to leet (keeping in mind to use the same capitalization as before)
> govtech-csg{R3g157ry_H1v3} 

