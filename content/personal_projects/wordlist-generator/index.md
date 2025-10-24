---
title: "password generator & wi-fi hacking 🛜"
date: 2021-11-01
summary: "A network security learning project using Kali Linux and a custom Python password generator."
tags: ["Personal", "Python", "Hacking"]
---

This project was done purely for fun and curiosity's sake. Check it out **[on GitHub](https://github.com/andrew-data-dev/WordlistGenerator)**!

---

There are plenty of common password lists available online, but I wanted to build a solution that generates custom, targeted wordlists based on specific keywords. The goal was to feed these tailored lists into Aircrack-ng on Kali Linux for network security testing.

Once you've deauthenticated a connection and captured the WPA handshake using the Aircrack-ng suite, you can run through your wordlists offline to attempt password recovery. For example, if I wanted to test the password strength for a local coffee shop named Joe's Java, I might start with a keyword list like this:

> coffee, joe, java, bean

I'd then run my Python script with those keywords as input. The script performs a variety of transformations to generate an extensive password list, including:

- Substituting common numbers and symbols for characters (e.g., "e" becomes "3")
- Testing different capitalization patterns
- Appending common numbers and symbols to the beginning and end of each word

The script then generates various combinations of these transformations:

- Capitalization combined with numbers, symbols, or substitutions
- Substitutions combined with numbers, symbols, or both
- Numbers and symbols in different orders and combinations
- All possible permutations of the above

This layered approach creates a comprehensive, targeted wordlist that accounts for common password creation patterns, making it far more effective than generic password lists for authorized security testing purposes.

My script would take the word "coffee" and generate 12,502 possible passwords. The first 300 passwords generated from the keyword "coffee":

> coffee Coffee cOffee coFFee coffEE COFFEE c0ffee coff3e coff33 coffe3 coffee123 123coffee coffee234 234coffee coffee345 345coffee coffee456 456coffee coffee567 567coffee coffee678 678coffee coffee789 789coffee coffee890 890coffee coffee321 321coffee coffee432 432coffee coffee534 534coffee coffee654 654coffee coffee765 765coffee coffee876 876coffee coffee987 987coffee coffee098 098coffee coffee1234 1234coffee coffee2345 2345coffee coffee3456 3456coffee coffee4567 4567coffee coffee5678 5678coffee coffee6789 6789coffee coffee7890 7890coffee coffee4321 4321coffee coffee5432 5432coffee coffee6543 6543coffee coffee7654 7654coffee coffee8765 8765coffee coffee9876 9876coffee coffee0987 0987coffee coffee12345 12345coffee coffee54321 54321coffee coffee123456 123456coffee coffee654321 654321coffee coffee69 69coffee coffee420 420coffee coffee00 00coffee coffee000 000coffee coffee11 11coffee coffee111 111coffee coffee22 22coffee coffee222 222coffee coffee! coffee!! coffee!!! coffee!@ coffee!@# Coffee123 Coffee123! Coffee123!! Coffee123!!! Coffee123!@ Coffee123!@# 123Coffee 123Coffee! 123Coffee!! 123Coffee!!! 123Coffee!@ 123Coffee!@# Coffee234 Coffee234! Coffee234!! Coffee234!!! Coffee234!@ Coffee234!@# 234Coffee 234Coffee! 234Coffee!! 234Coffee!!! 234Coffee!@ 234Coffee!@# Coffee345 Coffee345! Coffee345!! Coffee345!!! Coffee345!@ Coffee345!@# 345Coffee 345Coffee! 345Coffee!! 345Coffee!!! 345Coffee!@ 345Coffee!@# Coffee456 Coffee456! Coffee456!! Coffee456!!! Coffee456!@ Coffee456!@# 456Coffee 456Coffee! 456Coffee!! 456Coffee!!! 456Coffee!@ 456Coffee!@# Coffee567 Coffee567! Coffee567!! Coffee567!!! Coffee567!@ Coffee567!@# 567Coffee 567Coffee! 567Coffee!! 567Coffee!!! 567Coffee!@ 567Coffee!@# Coffee678 Coffee678! Coffee678!! Coffee678!!! Coffee678!@ Coffee678!@# 678Coffee 678Coffee! 678Coffee!! 678Coffee!!! 678Coffee!@ 678Coffee!@# Coffee789 Coffee789! Coffee789!! Coffee789!!! Coffee789!@ Coffee789!@# 789Coffee 789Coffee! 789Coffee!! 789Coffee!!! 789Coffee!@ 789Coffee!@# Coffee890 Coffee890! Coffee890!! Coffee890!!! Coffee890!@ Coffee890!@# 890Coffee 890Coffee! 890Coffee!! 890Coffee!!! 890Coffee!@ 890Coffee!@# Coffee321 Coffee321! Coffee321!! Coffee321!!! Coffee321!@ Coffee321!@# 321Coffee 321Coffee! 321Coffee!! 321Coffee!!! 321Coffee!@ 321Coffee!@# Coffee432 Coffee432! Coffee432!! Coffee432!!! Coffee432!@ Coffee432!@# 432Coffee 432Coffee! 432Coffee!! 432Coffee!!! 432Coffee!@ 432Coffee!@# Coffee534 Coffee534! Coffee534!! Coffee534!!! Coffee534!@ Coffee534!@# 534Coffee 534Coffee! 534Coffee!! 534Coffee!!! 534Coffee!@ 534Coffee!@# Coffee654 Coffee654! Coffee654!! Coffee654!!! Coffee654!@ Coffee654!@# 654Coffee 654Coffee! 654Coffee!! 654Coffee!!! 654Coffee!@ 654Coffee!@# Coffee765 Coffee765! Coffee765!! Coffee765!!! Coffee765!@ Coffee765!@# 765Coffee 765Coffee! 765Coffee!! 765Coffee!!! 765Coffee!@ 765Coffee!@# Coffee876 Coffee876! Coffee876!! Coffee876!!! Coffee876!@ Coffee876!@# 876Coffee 876Coffee! 876Coffee!! 876Coffee!!! 876Coffee!@ 876Coffee!@# Coffee987 Coffee987! Coffee987!! Coffee987!!! Coffee987!@ Coffee987!@# 987Coffee 987Coffee! 987Coffee!! 987Coffee!!! 987Coffee!@ 987Coffee!@# Coffee098 Coffee098! Coffee098!! Coffee098!!! Coffee098!@ Coffee098!@# 098Coffee 098Coffee! 098Coffee!! 098Coffee!!! 098Coffee!@ 098Coffee!@# Coffee1234 Coffee1234! Coffee1234!! Coffee1234!!! Coffee1234!@ Coffee1234!@# 1234Coffee 1234Coffee! 1234Coffee!!

The end result definitely needed refinement. To test the script, I had my wife set a simpler password for our home Wi-Fi network, but I was unable to crack it. Without knowing which keywords to use, I was essentially guessing blindly. Regardless, it was an enjoyable project that combined both coding and network security exploration.