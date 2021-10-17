# **EKHO BOT**

### **Video Demo:** https://youtu.be/o_AwcGfdv3Q

### **Description:** Ekho Bot is a discord bot that has several utilities. It mainly aims to vocalize quotes, answer questions, and play music. All functions are called with double semicolon (`;;`) to use the bot.

* Quote Command

    > Quotes are the main function of the Ekho. The bot can add new quotes to the quote database, delete them, list them, and change publicity of them, as well as vocalize them. `;;quote` is the command of this function.

    - `;;quote add u/p <a quote>`: This message adds the quote to the database with the given publicity (u for unpublic, p for public).
    - `;;quote list`: This message lists all the public quotes and quotes created in the message's server. It shows the id, the quote, the author and the publicity.
    - `;;quote del <id of a quote>`: This message deletes the quote with the id from the databases. The user must have permission to do this.
    - `;;quote pub u/p <id of a quote>`: This message alters publicitiy of the quote with the id. It automatically sets the present's opposite. The user must have permission to do this.
    - `;;quote tell <id of a quote>`: This message vocalize the quote with the id via connecting to the user's channel.

* Ask Command

    > Ask is another function of the Ekho. Users can ask any questions to the bot via both texts and voice inputs, and the bot searches these in the Google Search. If there is a quick answer the bot connect to the channel and tell the answer. Otherwise, it directs the user to the search link. `;;ask` is the command of this function.

    - `;;ask -t <a question>`: This message asks the question to the bot. After searching the question in the Google, Ekho give the answer either with voice or text outputs.
    - `;;ask -v`: This message calls the bot to the user's channel, and the user asks the question verbally. After searching the question in the Google, Ekho give the answer either with voice or text outputs.

* Music Command

    > Music is the last function of the Ekho. Users can play songs via either their urls or names, create playlists and add songs into these and delete songs from these, as well as change publicity of playlists, and add songs into their favorites and delete songs from it. They can also play their playlists and favorites. `;;music` is the command of this function.

    - `;;music play -m <name or url of a song>`: This message plays the songs. The bot finds the song via Google Search in case the command is used with the url.

    - `;;music play -p <name of a playlist>`: This message plays the songs from the playlist. User must have permission over the playlist to do this.

    - `;;music play -f`: This message plays the user's favorite songs.

    - `;;music stop`: This message stops music.

    - `;;music add -p <name of playlisy> -m <name of a song>`: This message adds the song to the playlist. User must have permission over the playlist to do this.

    - `;;music add -f <name of a song>`: This message adds the song to the user's favorites.

    - `;;music del -p <name of a playlist> -m <name of a song>`: This message deletes the song from the playlist. User must have permission over the playlist to do this.

    - `;;music del -f <name of a song>`: This message deletes the song from the user's favorites.

    - `;;music priv <name of a playlist>`: This message changes privacy of the playlist. It automatically sets the present's opposite. The user must have permission over the playlist to do this.

    - `;;music playlist <name of a playlist>`: This message lists the songs in the playlist from the playlist database. The user must have permission over the playlist to do this.

    - `;;music favorite`: This message lists the user's favorites.

## **Project Files and Folders**
***
1. ### **Databases**
    > Databases file has 3 different databases which are used in `quote` and `music` functions. All databases has created by using **SQL**.

    - **favorites.db:** This database contains one table also called as *favorites* which consists of 3 rows: *music*, *url*, and *user*. The table stores user's favorite songs in *music* row, url of these songs in *url* row, and name of the user in *user* row. It is used in `music` function.

    - **playlists.db:** This database contains two tables called as in turn *musics* and *playlists*. The latter is designed to store playlists, whilst the former stores songs in these playlists. 
        > *playlists* consists of 4 rows: *id*, *name*, *owner*, and *privacy*. The first row is the primary key for playlists, the second one is the name of the playlist, the third one is the creator of the playlist, and the last one indicates publicity (unpublic or public). 
        
        > *musics* consists of 3 rows: *name*, *url*, and *playlistID*. The first row is the name of the song, the second one is the url of this song, and the last one is the foreign key related with *id* row in *playlists* table.

    - **quote.db:** This database contains one table named same as the database name. *quote* table consists of 6 rows: *id*, *author*, *text*, *ServerID*, and *publicity*. The first row is the primary key for quotes, the second one is the creator of the quote, the third one stands for the content of the message that is saved as the quote, the fourth one is the ID of the server the message is sent, and the last one indicates publicity (unpublic or public).

2. ### **Python Files**
    > Python is the fundamental language used in this project. There are 2 Python files, one of which includes functions for the other. **utilities.<area>py** is the helper file for **application.<area>py**. The former's functions creates responses to users' messages and the latter utilizes these responses to decide how to act.

    - **application.<area>py:** This file contains main codes. Based on users' messages starting with the bot's indicator "`;;`", these codes decide how bot should response and act.
        - Implication of libraries, functions, and **utilities.<area>py**.

           ```python
            import discord
            import os
            import time
            from youtube_dl import YoutubeDL
            from dotenv import load_dotenv

            from utilities import music, quote, ask
            ```
        - Defination of discord client and check for whether the bot is ready to run. Functions are defined asynchronously in order to ensure proper usage of Ekho.

            ```python
            ## Use discord client as client
            client = discord.Client()

            ## Ensure that the bot is logged in
            @client.event 
            async def on_ready():
                print("Legendary {0.user} has appeared".format((client)))
            ```
        - Utilities of Ekho -*quote, ask, and music*- are run beyond the `on_message` function. If message is not sent from the bot itself, utilities are run.

            ```python
            ## Utilities
            @client.event
            async def on_message(message):
                
                ## Do not run functions if message is from the bot itself
                if message.author == client.user:
                    return

                else:
                    ... 
            ```
        - Check what the message starts with and if it is "`;;quote `", declare **response** variable as the output of `quote` function from **utilities.<area>py**. `quote` function can output diverse data types which are *string*, *list*, and *integer*. Based on data type of **response** via `isinstance` function, decide what to do. In case **response** is a string, just send the **response** to the channel.

            ```python
                    ## Based on commands run different functions
                    ## Quote command
                    if message.content.startswith(";;quote "):
                        
                        ## Take quotes and store and vocalize them
                        response = quote(message)

                        if isinstance(response, str) == True: ## Add, change, and delete as well as unauthorized tell command
                            await message.channel.send(response)

                        ...

                        else: ## If there is not proper usage of ;;quote
                            await message.channel.send("Quote does not have such a command. It supports **'add', 'list', 'tell', 'del', and 'pub'**. For detailed usage, use **';;help'**.")    
            ```      
        - **response's** being a list means that a user wants to list quotes on the database. The list is designed as `[ID, Author, Quote, Server ID, Publicity]`. If length of the list equals to zero, it is said that there is not any quotes. Otherwise, send messages to the channel.

            ```python
                        elif isinstance(response, list) == True: ## List command
                            if len(response) == 0: ## In case there is not any quotes to be shown
                                await message.channel.send("Your list consists of endless steppes. Try to add new quotes to revive and green these steppes!")
                            else:    
                                for item in response:
                                    if item[4] == "u": ## Unpublic quotes
                                        await message.channel.send(f"{item[0]}: {item[2]} --- from {item[1]} -- Unpublic")
                                    else: ## Public quotes
                                        await message.channel.send(f"{item[0]}: {item[2]} --- from {item[1]} -- Public")
            ```
        - If **response** is an integer, a quote whose ID is **response** must be vocalized in the user's voice channel. Firstly, it checks whether the user is in a voice channel, and if the user is in, it plays the audio. `voice.channel.connect()` connects the bot to the channel. `discord.FFmpegPCMAudio` determines the source, and `.play(source)` plays the audio. Lasty, the bot should wait to disconnect until audio is over.

            ```python
                        elif isinstance(response, int) == True: ## Tell command                
                            voice = message.author.voice
                            if voice != None and voice.channel != None: ## Whether user is in a voice channel or not
                                channel = voice.channel.name
                                vc = await voice.channel.connect() ## Connect the bot to the channel
                                source = discord.FFmpegPCMAudio(f"quotes/{response}.mp3") ## Audio file of quote
                                vc.play(source)
                                
                                while vc.is_playing(): ## Wait while playing the audio
                                    time.sleep(.1)

                                vc = await message.guild.voice_client.disconnect() ## Disconnect the bot
                            else:
                                await message.channel.send("You need to be in a voice channel to listen quotes. Right? Come on, join it and try again!")
            ```
        - If the message starts with "`;;ask `", check whether it is text input (`-t`) or voice input (`-v`). Declare **answer** as output of `ask` function from **utilities.<area>py**. If **answer** is Google Search URL, there are not any quick answers. Therefore, Ekho must direct the user to check the URL sent into the message channel. In order to direct the user, Ekho joins to the user's channel and plays **direct.mp3**. To play audio, the bot follows the aforementioned procedure.

            ```python
                    ## Ask command
                    elif message.content.startswith(";;ask "):

                        if message.content.startswith(";;ask -t "): ## Text input
                            answer = ask(message) ## Ask function to ask questions to the Google

                            if answer.startswith("https://www.google.com/search?q="): ## There are not any quick answers

                                voice = message.author.voice
                                if voice != None and voice.channel != None: ## Whether user is in a voice channel or not
                                    channel = voice.channel.name
                                    vc = await voice.channel.connect() ## Connect the bot to the channel
                                    source = discord.FFmpegPCMAudio("answers/direct.mp3") ## Audio file to direct user to the url
                                    vc.play(source)

                                    while vc.is_playing(): ## Wait while playing the audio
                                        time.sleep(.1)

                                    vc = await message.guild.voice_client.disconnect() ## Disconnect the bot

                                    await message.channel.send(f"Please, check {answer} for your answer!")

                                else:
                                    await message.channel.send(f"Please, check {answer} for your answer!")

                        ...

                        else: ## If there is not proper usage of ;;ask
                            await message.channel.send("Ask does not have such a command. It supports 'v' for voice input and 't' for text input. For detailed usage, use ';;help'.")            
            ```
        - If there is a quick answer, the bot joins the channel and plays the **answer.mp3** file which is created in `ask` function. To play the audio, the bot follows the aforementioned procedure, yet in the end, **answer.mp3** file is removed with `os.remove` function. If the user is not in a voice channel, Ekho just sends the answer to the message channel. 

            ```python
                            else: ## There is a quick answer
                                
                                voice = message.author.voice
                                if voice != None and voice.channel != None: ## Whether user is in a voice channel or not
                                    channel = voice.channel.name
                                    vc = await voice.channel.connect() ## Connect the bot to the channel
                                    source = discord.FFmpegPCMAudio("answers/answer.mp3") ## Audio file of the answer
                                    vc.play(source)

                                    while vc.is_playing(): ## Wait while playing the audio
                                        time.sleep(.1)

                                    vc = await message.guild.voice_client.disconnect() ## Disconnect the bot

                                else:
                                    await message.channel.send(f"What a pity with you in that you are not in a voice channel to listen the answer! However, your answer is **'{answer}'**.")

                                os.remove(f"answers/answer.mp3") ## Delete the answer    
            ```
        - If the user wants to give voice input, Ekho checks whether the user is in a voice channel. If the user is in, Ekho joins to the channel and plays **ask.mp3** by following the aforementioned procedure. While and after doing this, there may be some errors. Since `speech_recognition` is availed to take voice inputs by using Google, an error might occur when Google do not respond to the request. In this case, Ekho plays the **requestErr.mp3**. Furthermore, Ekho may not understand the user's voice fluently and Ekho plays the **valueErr.mp3** to point out that. 

            ```python
                        elif message.content.startswith(";;ask -v"): ## Voice input
                
                            voice = message.author.voice
                            if voice != None and voice.channel != None: ## Whether user is in a voice channel or not
                                channel = voice.channel.name
                                vc = await voice.channel.connect() ## Connect the bot to the channel
                                source = discord.FFmpegPCMAudio("answers/ask.mp3") ## Audio file to tell the user that one can ask now
                                vc.play(source)

                                while vc.is_playing(): ## Wait while playing the audio
                                    time.sleep(.1)

                                answer = ask(message)

                                if isinstance(answer, int): ## Error situations
                                    if answer == 2: ## Request error
                                        source = discord.FFmpegPCMAudio("answers/requestErr.mp3") ## Audio file of request error
                                        vc.play(source)

                                        while vc.is_playing(): ## Wait while playing the audio
                                            time.sleep(.1)
                                    
                                    elif answer == 3: ## Value error
                                        source = discord.FFmpegPCMAudio("answers/valueErr.mp3") ## Audio file of value error
                                        vc.play(source)

                                        while vc.is_playing(): ## Wait while playing the audio
                                            time.sleep(.1)

                            ...

                            else:
                                await message.channel.send("Hmm, There is an genius among us who can try to give a voice input WHILE ONE IS NOT IN A VOICE CHANNEL. Come on, are you joking??")
            ```                                
        - If there is not any error in voice input, same procedure as text inpur is followed. 

            ```python
                                    else: ## There is not any error
                                        if answer.startswith("https://www.google.com/search?q="): ## There are not any quick answers

                                            source = discord.FFmpegPCMAudio("answers/direct.mp3") ## Audio file to direct user to the url
                                            vc.play(source)

                                            while vc.is_playing(): ## Wait while playing the audio
                                                time.sleep(.1)

                                            await message.channel.send(f"Please, check {answer} for your answer!")

                                        else: ## There is a quick answer

                                            source = discord.FFmpegPCMAudio("answers/answer.mp3") ## Audio file of the answer
                                            vc.play(source)

                                            while vc.is_playing(): ## Wait while playing the audio
                                                time.sleep(.1)

                                            os.remove(f"answers/answer.mp3") ## Delete the answer

                                    vc = await message.guild.voice_client.disconnect() ## Disconnect the bot

            ```
        - If the message starts with "`;;music `", FFmpeg and YDL options are set, and **response** variable is declared as output of `music` function from **utilitites.<area>py**. If **response** is a string, it is sent into the channel, and also if the user wants to stop music, the bot is disconnected. 

            ```python
                    ## Music command
                    elif message.content.startswith(";;music "):

                        ## FFmpeg and YDL options
                        FFMPEG_OPTIONS = {'before_options': '-reconnect 1 -reconnect_streamed 1 -reconnect_delay_max 5', 'options': '-vn'}
                        YDL_OPTIONS = {'format': 'bestaudio'}

                        response = music(message)

                        if isinstance(response, str): ## String returns

                            if message.content.startswith(";;music stop"): ## Stop the music
                                vc = await message.guild.voice_client.disconnect() ## Disconnect the bot

                            await message.channel.send(response)

                        ...

                        else: ## If there is not proper usage of ;;music
                            await message.channel.send("Music does not have such a command. It supports **'play', 'add', 'stop', 'del', 'favorite', 'playlist', and 'priv'**. For detailed usage, use **';;help'**.")    

            ```
        - If response is a list and the message starts with "`;;music play `", a music, playlist, or favorites should be played. If the first item of the list is not a URL, Ekho only plays a song; otherwise, Ekho plays songs in a for loop. This list is designed as [URL, Name of the Song]. To play a song, `YoutubeDL` is used. `YoutubeDL` codes are quoted from the Youtube Channel [Max A](https://www.youtube.com/watch?v=jHZlvRr9KxM&t=280s). While playing music, `asyncio.sleep()` is used, unlike `ask` and `quote` codes, in order to wait until the song is finished. After that, Ekho is disconnected from the channel. 

            ```python
                        elif isinstance(response, list): ## List returns
                            
                            if message.content.startswith(";;music play "):
                                voice = message.author.voice
                                if voice != None and voice.channel != None: ## Whether user is in a voice channel or not
                                    channel = voice.channel.name
                                    vc = await voice.channel.connect() ## Connect the bot to the channel

                                    if len(response) == 1 or response[1].startswith("https://"): ## Playlists or favorites will be played
                                        for musics in response:
                                            await message.channel.send(f"{musics} is now playing!")
                                            with YoutubeDL(YDL_OPTIONS) as ydl:
                                                info = ydl.extract_info(musics, download=False)
                                                url2 = info["formats"][0]["url"]
                                                source = await discord.FFmpegOpusAudio.from_probe(url2, **FFMPEG_OPTIONS)
                                                vc.play(source)

                                                while vc.is_playing() == True or not message.content.startswith(";;music stop"):
                                                    await asyncio.sleep(.1)

                                                vc = await message.guild.voice_client.disconnect() ## Disconnect the bot
                                                
                                    else: ## In case just a music will be played
                                        await message.channel.send(f"**{response[1]}** is now playing! {response[0]}")

                                        with YoutubeDL(YDL_OPTIONS) as ydl:
                                            info = ydl.extract_info(response[0], download=False)
                                            url2 = info["formats"][0]["url"]
                                            source = await discord.FFmpegOpusAudio.from_probe(url2, **FFMPEG_OPTIONS)
                                            vc.play(source)
                                            
                                            while vc.is_playing() == True or not message.content.startswith(";;music stop"):
                                                await asyncio.sleep(.1)

                                            vc = await message.guild.voice_client.disconnect() ## Disconnect the bot    

                                    else:
                                        await message.channel.send("You need to be in a voice channel to listen music. Otherwise, you should have magical ears. If you don't, join it and try again!")             
            ```
        - If **response** is a list, yet the message does not starts with "`;;music play `", the user's favorites or playlists need to be shown. This list is designed as [Favorites or Playlist Name, Name of the Song].

            ```python
                            else: ## Favorites or playlists will be shown
                                await message.channel.send(f"-- {response[0]} --")
                                for i in range(1, len(response)):
                                    await message.channel.send(f"{i}: {response[i]}")
            ```
        - If the message starts with "`;;help`", send a guide to the message channel. And if the message starts with "`;;`", yet there is not a proper usage, tell the user proper usage.

            ```python
                    ## Help command
                    elif message.content.startswith(";;help"):
                        
                        await message.channel.send("-----***quote***-----")

                        ## Quote helps
                        await message.channel.send("**;;quote add u/p <your quote>**                                                            --> Add quotes to list. **u** is for unpublic and **p** is for public.") ## Add quote
                        await message.channel.send("**;;quote del <id of the quote>**                                                              --> Delete the quote with the id from the list. You must have permission over the quote to do this.") ## Delete quote
                        await message.channel.send("**;;quote list**                                                                                               --> List all of the available quotes.") ## List quote
                        await message.channel.send("**;;quote tell <id of the quote>**                                                              --> Vocalize the given quote. You must have the permission over the quote to do this.") ## Vocalize quote
                        await message.channel.send("**;;quote pub u/p <id of the quote>**                                                     --> Change publicity of the given quote. You must have permission over the quote to do this.") ## Change publicity of a quote
                            
                        await message.channel.send("-----***ask***-----")

                        ## Ask helps
                        await message.channel.send("**;;ask -t <your question>**                                                                       --> Ask any questions and EkhoBot will answer them.") ## Text input questions
                        await message.channel.send("**;;ask -v**                                                                                                       --> Ask questions via voice inputs.") ## Voice input questions
                            
                        await message.channel.send("-----***music***-----")

                        ## Music helps
                        await message.channel.send("**;;music play -m <name or url of the song>**                                         --> Play the given song.") ## Play music
                        await message.channel.send("**;;music play -p <name of the playlist>**                                              --> Play the given playlist. You must have permission over the playlist to do this.") ## Play a playlist
                        await message.channel.send("**;;music play -f**                                                                                           --> Play favorites.") ## Play user's favorites
                        await message.channel.send("**;;music add -p <name of the playlist> -m <name of the song>**  --> Add a song to the playlist. You must have permission over the playlist to do this.") ## Add songs to a playlist
                        await message.channel.send("**;;music add -f <name of the song>**                                                   --> Add a song to your favorites.") ## Add songs to user's favorites
                        await message.channel.send("**;;music del -p <name of the playlist> -m <name of the song>**   --> Delete a song from the playlist. You must have permission over the playlist to do this.") ## Delete songs from a playlist
                        await message.channel.send("**;;music del -f <name of the song>**                                                      --> Delete a song from your favorites.") ## Delete songs from user's favorites
                        await message.channel.send("**;;music priv <name of the playlist>**                                                    --> Change privacy of the playlist. You must have permission over the playlist to do this.") ## Change privacy of a playlist
                        await message.channel.send("**;;music playlist <name of the playlist>**                                              --> List songs of the playlist. You must have permission over the playlist to do this.") ## List a playlist
                        await message.channel.send("**;;music favorite**                                                                                        --> List your favorite songs.") ## List user's favorites
                        await message.channel.send("**;;music stop**                                                                                           --> Stop the music.") ## Stop music
                        ## Credits
                        await message.channel.send("----------------------")
                        await message.channel.send("*EkhoBot has created by **Kspace#0008**. For any contact DM him.*")

                    ## Wrong usage
                    elif message.content.startswith(";;"):
                        await message.channel.send("EkhoBot does not have such a command. It supports **;;quote**, **;;ask**, **;;music** and **;;help** commands. Check **;;help** for detalied usage.")
            ```
        - Last lines of codes to tell the program that the discord bot with the TOKEN in the **.env** file should be used.

            ```python
            ## Tell program which bot will be used in program
            load_dotenv("C:/Users/Kerem/Documents/Python/Environments/.env")
            client.run(os.getenv("TOKEN"))
            ```

    - **utilities.<area>py:** This file includes `quote`, `ask`, and `music` functions used in **applications.<area>py**.
        - Implication of libraries and functions.

            ```python
            import sqlite3
            import pyttsx3
            import os
            import requests
            import speech_recognition
            from bs4 import BeautifulSoup
            ```
        - `quote` function to be used in `;;quote` command. It takes **message** as the input and give diverse outputs based on the input. `sqlite3.connect()` attaches quote database to the file, and `.cursor()` helps to run **SQLite** commands. **author**, **serverID**, **three**, and **four** are the variables taken from **message**. **three** and **four** are used to determine what the user wants to do.

            ```python
            ## Quote function to store and vocalize users' quotes
            def quote(message):
                
                ## Connect the database
                db = sqlite3.connect("databases/quote.db")
                cur = db.cursor()
                        
                ## Assing message determiner and author
                author = message.author
                three = message.content[8:12] ## For list, add, delete, and pub with a space commands
                four = message.content[8:13] ## For tell with a space command
                serverID = message.guild.id
            ```
        - If **three** equals to "add", a quote should be added into **quote.db** database. If the usage is accurate, the quote is inserted into the database with **author**, **text**, **ServerID**, and **publicity**. After that, the ID of the inserted quote is found with `SELECT` command and added into the `IDs` list. Since the ID of the quote must be in the last place in the list, `IDs[-1]` is used to find the ID and send a response to the **applications.<area>py**.

            ```python
                ## Store quotes in quote.db with the message's content and message's author
                if three == "add ":
                    checker = message.content[:14]
                    text = message.content[14:]
                    publicity = message.content[12:13]

                    if checker == ";;quote add p " or checker == ";;quote add u ":
                        cur.execute("INSERT INTO quote (author, text, ServerID, publicity) VALUES(?, ?, ?, ?)", (author.name, text, serverID, publicity))
                            
                        idOfText = cur.execute("SELECT id FROM quote WHERE author = ? AND text = ?", (author.name, text))
                        IDs = []
                                
                        db.commit()
                        
                        for row in idOfText:
                            IDs.append(row[0])
                    
                        response = f"Your text has been saved with the id of {IDs[-1]}"
                    
                    else:
                        response = "Wrong usage!!! Check ';;help' for the guide."
                    
                    return response
            ```
        - If **three** equals to "list", quotes that are created in the message's server and that are public should be listed. Quotes are selected from **quote.db** with `SELECT`command and appended into the `quotes` list which is returned to the **applications.<area>py**.

            ```python
                ## List quotes by their IDs, authors, and texts
                elif three == "list":
                    quote = cur.execute("SELECT * FROM quote WHERE ServerID = ? OR publicity = 'p'", (serverID,))
                    
                    quotes = []
                    
                    db.commit()
                    
                    for row in quote:
                        quotes.append(row)
                        
                    return quotes
            ```
        - If **three** equals to "del ", a quote should be deleted from **quote.db**. ID of the quote is selected with `SELECT` command and if this quote exists, and the user has permission to delete this quote, the quote is deleted by using `DELETE` command. In case an mp3 file for this quote exists, it is deleted by using `os` library, and a confirmation response is returned.

            ```python
                ## Delete quotes based on their IDs
                elif three == "del ":
                    ID = message.content[12:]
                    
                    quote = cur.execute("SELECT author, ServerID FROM quote WHERE id = ?", (ID,))
                    quoteAuthor = None
                    serID = None
                    
                    if quote == None:
                        response = "There is not such a quote :("

                    else:     
                        for row in quote:
                            quoteAuthor = row[0]
                            serID = row[1]
                            
                        
                        if  author.guild_permissions.administrator != True or quoteAuthor != author.name or serID != serverID: ## Check whether the user has permission to delete quotes 
                            response = "You don't have permission to delete this quote. You are just an ordinary joe :("
                            
                        else:
                            cur.execute("DELETE FROM quote WHERE id = ?", (ID,))

                            if os.path.isfile(f"quotes/{ID}.mp3") == True:
                                os.remove(f"quotes/{ID}.mp3")
                                
                            response = f"Your quote {ID} has flown into infinity"
                    
                    db.commit()
                    
                    return response
            ```
        - If **three** equals to "pub ", publicity of a quate should be changed. ID of this quote selected with `SELECT` command, and if this quote exists, and the user has permission to do this task, publicity of the quote is changed with `UPDATE` command, and a confirmation response is returned.

            ```python
                ## Change publicity of a quote
                elif three == "pub ":
                    newPublicity = message.content[12:13]
                    ID = message.content[14:]

                    quote = cur.execute("SELECT author, ServerID, publicity FROM quote WHERE id = ?", (ID,))
                    publicity = None
                    serID = None
                    quoteAuthor = None
                    
                    if quote == None:
                        response = "There is not such a quote :("

                    else:    
                        for row in quote:
                            quoteAuthor = row[0]
                            serID = row[1]
                            publicity = row[2]

                        if quoteAuthor != author.name or serID != serverID:
                            response = "You don't have permission to change publicitiy of this quote. You should be the author and quote must be created in the current server"

                        else:
                            cur.execute("UPDATE quote SET publicity = ? WHERE id = ?", (newPublicity, ID))
                            response = f"Publicity of quote {ID} has changed. Be careful! In case it is public, you are not safe anymore!!"
                    
                    db.commit()

                    return response
            ```
        - If **four** equals to "tell ", a quote should be vocalized. The ID of this quote is selected with `SELECT` command, and if this quote exists, and the user has permission to listen it, an mp3 file is created with `pyttsx3` library and the ID is returned. If there is a problem, a response is returned.

            ```python
                ## Vocalize the quote
                elif four == "tell ":
                    ID = message.content[13:]

                    quote = cur.execute("SELECT text, ServerID, publicity FROM quote WHERE id = ?", (ID,))
                    text = None
                    serID = None
                    pub = None
                    
                    db.commit()
                    
                    if quote == None:
                        response = "There is not such a quote :("

                    else:    
                        for row in quote:
                            text = row[0]
                            serID = row[1]
                            pub = row[2]

                        if serID == serverID or pub == "p":
                            if not os.path.isfile(f"quotes/{ID}.mp3") == True: ## If this quote has not ever been vocalized, create mp3 file
                    
                                engine = pyttsx3.init()

                                rate = engine.getProperty('rate')
                                engine.setProperty('rate', rate - 75)
                                volume = engine.getProperty('volume')
                                engine.setProperty('volume', volume+100)

                                engine.save_to_file(text, f"quotes/{ID}.mp3")

                                engine.runAndWait()

                            return int(ID)    
                        
                        else:
                            response = "You cannot listen this highly secured quote. It may contain severe informations about aliens -_-"

                    return response

                ## Wrong command usage
                else:
                    return None
            ```
        - `ask` function to be used in `;;ask` command. It takes **message** as the input and give diverse outputs based on the input. **two** and **three** are variables used to determine the task.

            ```python
            ## Ask function to ask questions to Google Search and vocalize answers if they are quick answer; otherwise, direct users to answer links
            def ask(message):

                two = message.content[6:8] ## Two digits commands -v
                three = message.content[6:9] ## Two digits commands and a space -t
            ```
        - If **three** equals to "-t ", the user desires to ask question via text input. Firstly, a URL is created by replacing spaces with "%20" and combining the question with *https://www.google.com/search?q=*. Secondly, thanks to `request` library, html file of the URL is requested and stored in **request** variable. Finally, using `BeautifulSoup` function with `lxml` library, any quick answer is seeked with `.find` method with `"div", class_="Z0LcW XcVN5d"` parameters since these html tags includes Google quick answers in case they exists.

            ```python
                if three == "-t ": ## Text question

                    ## Define the question and search url
                    question = message.content[9:].replace(" ", "%20")
                    url = f"https://www.google.com/search?q={question}"

                    ## Make request to Google
                    headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.186 Safari/537.36'}
                    request = requests.get(url, headers=headers)

                    ## Create soup to check whether a quick answer exists or not
                    soup = BeautifulSoup(request.text, "lxml")
                    quickAnswer = soup.find("div", class_="Z0LcW XcVN5d")
            ```
        - If there are not any quick answers, the URL is returned, yet if there is any, by using `pyttsx3` library, **answer.mp3** file is created and the quick answer is returned.

            ```python
                    if quickAnswer == None: ## In case there are not any quick answers
                    answer = url

                    return answer

                    else: ## There is a quick answer
                        answer = f"The answer is {quickAnswer.text}"
                
                        engine = pyttsx3.init()

                        rate = engine.getProperty('rate')
                        engine.setProperty('rate', rate - 75)
                        volume = engine.getProperty('volume')
                        engine.setProperty('volume', volume+100)

                        engine.save_to_file(answer, f"answers/answer.mp3")

                        engine.runAndWait()
                        
                        return quickAnswer.text
            ```
        - If **two** equals to "-v", the user desires to ask question via voice input. Firstly, a recognizer is created by using `speech_recognition` library, and loop is continued until an input is granted or an error occurs. To take voice input, `Microphone()` function is used as **mic** variable, and `.adjust_for_ambient_noise()` method is used to diminish ambient noise. Voice is listened with `.listen(mic)` method and it is transformed into text with `.recognize_google()` method. Lastly, spaces in the question replaced with "%20" to create the URL. If the recognizer cannot make request to Google, `RequestError` occurs, and if Google cannot transform audio to text, `ValueError` occurs.

            ```python
                elif two == "-v": ## Take voice input
                    
                    input = None
                    recog = speech_recognition.Recognizer()

                    while(input == None): ## Wait until input is granted

                        try:
                            
                            with speech_recognition.Microphone() as mic: ## Use microphone for input

                                recog.adjust_for_ambient_noise(mic, duration = 0.2) ## Adjust the noise in the environment

                                audio = recog.listen(mic) ## Take input

                                input = recog.recognize_google(audio) ## Speech to text translation thanks to Google
                                input = input.lower().replace(" ","%20")

                        except speech_recognition.RequestError:
                            return 2
                        except speech_recognition.UnknownValueError:
                            return 3
            ```  
        - After taking voice input, procedures to seek any quick answer and create audio file for it are exactly same as the above-mentioned text input procedures.

            ```python
                    url = f"https://www.google.com/search?q={input}"

                    ## Make request to Google
                    headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.186 Safari/537.36'}
                    request = requests.get(url, headers=headers)

                    ## Create soup to check whether a quick answer exists or not
                    soup = BeautifulSoup(request.text, "lxml")
                    quickAnswer = soup.find("div", class_="Z0LcW XcVN5d")

                    if quickAnswer == None: ## In case there are not any quick answers
                        answer = url
                        return answer

                    else: ## There is a quick answer
                        answer = f"The answer is {quickAnswer.text}"
                
                        engine = pyttsx3.init()

                        rate = engine.getProperty('rate')
                        engine.setProperty('rate', rate - 75)
                        volume = engine.getProperty('volume')
                        engine.setProperty('volume', volume+100)

                        engine.save_to_file(answer, f"answers/answer.mp3")

                        engine.runAndWait()

                        return quickAnswer.text

                else: ## Wrong command usage
                    return None
            ```
        - `music` function to be used in `;;music` command. It takes **message** as the input and give diverse outputs based on the input. `sqlite3.connect()` and `.cursor` are used to attach databases into the file. **author**, **three**, **four**, **seven**, and **eight** are variables taken from **message**. **three**, **four**, **seven**, and **eight** determines which task should be done.

            ```python
            ## Music function to play musics as well as create playlists and play them
            def music(message):

                ## Connect databases
                plist = sqlite3.connect("databases/playlists.db")
                pcur = plist.cursor()

                fav = sqlite3.connect("databases/favorites.db")
                fcur = fav.cursor()

                ## Assign variables
                author = message.author.name
                three = message.content[8:12] ## Three digit commands: add, del and four digit command stop
                four = message.content[8:13] ## Four digit commands: play, priv
                seven = message.content[8:16] ## Eight digit command: favorite
                eight = message.content[8:17] ## Eight digit command: playlist
            ```
        - `urlFinder()` is a function designed to find the Youtube URL of a song. Firstly, it takes **music** as input and create a search URL. With `request` and `bf4` libraries, name and URL of the song are seeked. The URL is stored in `a` tag and `class_="rGhul IHSDrd"` class, whilst song name is stored in `h3` tag and `class_="LC20lb MMgsKf"` class. If both the URL and name exists, the URL is appended to `url` list from `urls` dictionary, and song name is taken as the text not including last 10 character - "Singer - Song - Youtube" is the format of song name, and by excluding last 10 character, only singer and song is shown-. If there is not a song name found via `.find` method, **music** is used as name. Lastly, if the URL cannot be found, an error response is returned.

            ```python
                ## Find the youtube url of a music
                def urlFinder(music):
                    search = f'https://www.google.com/search?q={music.replace(" ", "%20")}'

                    ## Make request to Google
                    headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.186 Safari/537.36'}
                    request = requests.get(search, headers=headers)

                    ## Create soup to check whether a quick answer exists or not
                    soup = BeautifulSoup(request.text, "lxml")
                    urls = soup.find("a", class_="rGhul IHSDrd", href=True)
                    musicName = soup.find("h3", class_="LC20lb MMgsKf")

                    if musicName != None and urls != None:
                        url = urls["href"]
                        musicName = musicName.text[:-10]
                        listOf = [url, musicName]

                        return listOf

                    elif musicName == None and urls != None:
                        url = urls["href"]
                        musicName = music
                        listOf = [url, musicName]

                        return listOf            

                    else:
                        response = "Your song could not be found. Try Again!"
                        return response
            ```
        - If **eight** equals to "playlist ", songs in a playlist should be listed. Firstly, name of the playlist is assigned to **playlist** variable. After that, it is checked whether **playlist** exists or not. If it does, ID of the playlist and songs in this playlist are selected via `SELECT` command, and songs are appended to `musics` list which is returned to **applications.<area>py**.

            ```python
                if eight == "playlist ": ## List a playlist
                playlist = message.content[17:]

                pl = pcur.execute("SELECT id FROM playlists WHERE name = ? AND (owner = ? OR privacy = 'p')", (playlist, author))
                plist.commit()
                lenOfPl = 0
                for row in pl:
                    lenOfPl += 1

                if lenOfPl != 1: ## There is not such a playlist.
                    response = "There is not such a playlist."
                    return response

                else:
                    pl = pcur.execute("SELECT id FROM playlists WHERE name = ? AND (owner = ? OR privacy = 'p')", (playlist, author))
                    plist.commit()

                    id = None
                    for row in pl:
                        id = row[0]
                    
                    ms = pcur.execute("SELECT name FROM musics WHERE playlistID = ?", (id,))
                    
                    musics = [playlist]

                    for row in ms:
                        musics.append(row[0])

                    return musics
            ```
        - If **four** equals to "play ", a playlist, a song, or favorites of a user should be played. If it is a song, it is checked whether the URL of the song or name of the song is given. If it is the URL, a list consists of the URL and the text "Your Song" is returned to **applications.<area>py**, but if it is the name, a list created by `urlFinder(music)` is returned to **applications.<area>py**.

            ```python
                elif four == "play ": ## Play the music
                    determiner = message.content[13:16]

                    if determiner == "-m ": ## Play a music
                        
                        music = message.content[16:]
                        
                        if not music.startswith("https:"): ## Play a music via its name

                            listOf = urlFinder(music)

                        else: ## Play a music via its url
                            
                            listOf = [music, "Your Song"]

                        return listOf
            ```
        - If the user wants to play a playlist, it is firstly checked whether the playlist exists and there is any song inside it. In case there is not any problems, URLs of songs are selected from **musics** table in **playlists.db** database and appended into the `URLs` list which is returned.

            ```python
                    elif determiner == "-p ": ## Play a playlist
            
                        playlist = message.content[16:]

                        pl = pcur.execute("SELECT id FROM playlists WHERE name = ? AND (owner = ? OR privacy = ?)", (playlist, author, "p"))
                        plist.commit()
                        lenOfPl = 0
                        for row in pl:
                            lenOfPl += 1

                        if lenOfPl == 0: ## There is not such a playlist
                            response = "You don't have such a playlist."
                            return response

                        else:
                            pl = pcur.execute("SELECT id FROM playlists WHERE name = ? AND (owner = ? OR privacy = ?)", (playlist, author, "p"))
                            plist.commit()
                            id = None
                            for row in pl:
                                id = row[0]

                            ms = pcur.execute("SELECT url FROM musics WHERE playlistID = ?", (id,))
                            lenOfMs = 0
                            for row in ms:
                                lenOfMs += 1

                            if lenOfMs == 0: ## There is not any musics in the list
                                response = "Your playlist is empty."
                                return response

                            else:
                                ms = pcur.execute("SELECT url FROM musics WHERE playlistID = ?", (id,))
                                plist.commit()
                                lenOfMs = 0
                                URLs = []
                                for row in ms:
                                    URLs.append(row[0])
                                return URLs
            ```
        - If the user wants to play one's favorite songs, it is checked whether the user has any favorite song. If one has, favorite songs are selected from **favorites** table in **favorites.db** database and appended into the `URLs` list which is returned.

            ```python
                    elif determiner[:2] == "-f": ## Play favorites

                        fv = fcur.execute("SELECT url FROM favorites WHERE user = ?", (author,))
                        fav.commit()
                        lenOfFv = 0
                        for row in fv:
                            lenOfFv += 1

                        if lenOfFv == 0: ## There is not any favorites
                            response = "You don't have any favorite music. Try to add some!!"
                            return response

                        else:
                            fv = fcur.execute("SELECT url FROM favorites WHERE user = ?", (author,))
                            fav.commit()
                            URLs = []
                            for row in fv:
                                URLs.append(row[0])
                            return URLs

                    else: ## Wrong usage
                        return None   
            ```
        - If **three** equals to "stop", a basic response is returned to **applications.<area>py**.

            ```python
                elif three == "stop": ## Stop the music
                    response = "Music has stopped."
                    return response 
            ```
        - If **three** equals to "add ", a song should be added to a playlist or the user's favorites. If it should be added to a playlist, thanks to two determiners, "-p" and "-m", playlist and song name is found. After that, `urlFinder()` function is called to find URL of the song. In case it is not found, an error response is returned to **applications.<area>py**. Otherwise, it is checked whether the playlist exists or not. If it does not exist, it is created in **playlists** table in **playlists.db** database with `INSERT` command, and the song is inserted into **musics** table in **playlists.db** database with `INSERT` command. Finally, a confirmation response is returned to **applications.<area>py**.

            ```python
                elif three == "add ": ## Add to a playlist or favorites

                    determiner = message.content[12:15]

                    if determiner == "-p ": ## Add to a playlist

                        text = message.content[15:]

                        words = text.split()
                        
                        place = words.index("-m")
                        
                        playlistList = words[:place]
                        musicList = words[(place + 1):]

                        playlist =" ".join(playlistList) ## Find the playlist from the message
                        music = " ".join(musicList) ## Find the song from the message
                    
                        listOf = urlFinder(music)

                        if isinstance(listOf, str) == True:
                            return listOf

                        else:    
                            music = listOf[1]
                            url = listOf[0]

                            pl = pcur.execute("SELECT id FROM playlists WHERE name = ? AND owner = ?", (playlist, author))

                            lenOfPl = 0
                            for row in pl:
                                lenOfPl += 1

                            if lenOfPl == 0: ## The playlist does no exists

                                pcur.execute("INSERT INTO playlists(name, owner, privacy) VALUES(?, ?, ?)", (playlist, author, "u")) ## Create the playlist with default unpublicity
                                plist.commit()

                                pl = pcur.execute("SELECT id FROM playlists WHERE name = ? AND owner = ?", (playlist, author))
                                plist.commit()
                                id = None
                                for row in pl:
                                    id = row[0]

                                pcur.execute("INSERT INTO musics(name, url, playlistID) VALUES(?, ?, ?)", (music, url, id))
                                plist.commit()

                            else: ## Playlist exists
                                pl = pcur.execute("SELECT id FROM playlists WHERE name = ? AND owner = ?", (playlist, author))
                                plist.commit()
                                id = None
                                for row in pl:
                                    id = row[0]

                                pcur.execute("INSERT INTO musics(name, url, playlistID) VALUES(?, ?, ?)", (music, url, id))
                                plist.commit()

                            response = f"**{music}** has added to **{playlist}**!"
                            return response
            ```
        - If a song needs to be added into the user's favorites, it is firstly checked whether the song is already in favorites. If it is, a single response is returned. If it is not, the song is inserted into **favorites** table in **favorites.db** database with `INSERT`, and a confirmation response is returned.

            ```python
                    elif determiner == "-f ": ## Add to favorites
                    
                    music = message.content[15:]

                    if isinstance(urlFinder(music), str):
                        response = urlFinder(music)
                        return response

                    else:
                        url = urlFinder(music)[0]
                        music = urlFinder(music)[1]

                        fv = fcur.execute("SELECT music FROM favorites WHERE music = ? AND url = ? AND user = ?", (music, url, author))

                        lenOfFv = 0
                        for row in fv:
                            lenOfFv += 1

                        if lenOfFv == 1: ## The song is already in favorites
                            response = f"**{music}** is already in favorites."
                            return response

                        else:
                            fcur.execute("INSERT INTO favorites(music, url, user) VALUES(?, ?, ?)", (music, url, author))
                            fav.commit()
                            
                            response = f"**{music}** has added to favorites."
                            return response ## The song has added to favorites

                    else: ## Wrong usage
                        return None
            ```
        - If **three** equals to "del ", a song should be deleted from a playlist or the user's favorites. If it needs to be deleted from a playlist, two determiners, "-p" and "-m", are used to seek the playlist name and song name from the message. After that, a single error response is returned only if URL of the song is not found by `urlFinder()`, the playlist does not exists, or the song is not already in the playlist. Otherwise, delete the song from the playlist with `DELETE` command, and return a confirmation response.

            ```python
                elif three == "del ": ## Delete songs from a playlist or favorites
            
                    determiner = message.content[12:15]

                    if determiner == "-p ": ## Delete from a playlist
                        text = message.content[15:]

                        words = text.split()
                        
                        place = words.index("-m")
                        
                        playlistList = words[:place]
                        musicList = words[(place + 1):]

                        playlist =" ".join(playlistList) ## Find the playlist from the message
                        music = " ".join(musicList) ## Find the song from the message
                        URLs = urlFinder(music)
                        
                        if isinstance(URLs, str):
                            return URLs

                        else:    
                            url = URLs[0]
                            music = URLs[1]
                        
                            pl = pcur.execute("SELECT id FROM playlists WHERE name = ? AND owner = ?", (playlist, author))
                            plist.commit()
                            lenOfPl = 0
                            for row in pl:
                                lenOfPl += 1

                            if lenOfPl != 1: ## The playlist does not exist
                                response = "You don't have such a playlist."
                                return response

                            else:        
                                pl = pcur.execute("SELECT id FROM playlists WHERE name = ? AND owner = ?", (playlist, author))
                                plist.commit()
                                id = None
                                for row in pl:
                                    id = row[0]    

                                ms = pcur.execute("SELECT name FROM musics WHERE playlistID = ? AND name = ? AND url = ?", (id, music, url))
                                plist.commit()
                                lenOfMs = 0
                                for row in ms:
                                    lenOfMs += 1
                                
                                if lenOfMs == 0: ## The song is not in the playlist
                                    response = f"**{music}** is not in the playlist."
                                    return response

                                else:
                                    pcur.execute("DELETE FROM musics WHERE playlistID = ? AND name = ? AND url = ?", (id, music, url))
                                    plist.commit()
                                    response = f"**{music}** has deleted from **{playlist}**"
                                    return response
            ```
        - If a song needs to be deleted from the user's favorites, it is firstly checked whether the song is the user's favorites. If it is not a basic response is returned. If it is, the song is deleted with `DELETE` command, and a confirmation response is returned.

            ```python
                    elif determiner == "-f ": ## Delete from the favorites

                        if isinstance(urlFinder(message.content[15:]), str):
                            response = urlFinder(message.content[15:])
                            return response

                        else:    
                            music = urlFinder(message.content[15:])[1]
                            url = urlFinder(message.content[15:])[0]

                            fv = fcur.execute("SELECT music FROM favorites WHERE music = ? AND user = ? AND url = ?", (music, author, url))
                            fav.commit()
                            lenOfFv = 0
                            for row in fv:
                                lenOfFv += 1

                            if lenOfFv != 1: ## The song is not in the favorites
                                response = f"**{music}** is not in your favorites."
                                return response

                            else:
                                fcur.execute("DELETE FROM favorites WHERE music = ? AND user = ? AND url = ?", (music, author, url))
                                fav.commit()
                                response = f"**{music}** has deleted from your favorites."
                                return response
                    
                    else: ## Wrong usage
                        return None
            ```
        - If **four** eqauls to "priv ", privacy of a playlist should be changed. Firstly, it is checked whether the playlist exists or not by using `SELECT` command. If it does not exist, an information response is returned. If it does, privacy of the playlist is found with `SELECT` command and changed to adverse of current with `UPDATE` command.

            ```python
                elif four == "priv ": ## Change a playlist's privacy
                    playlist = message.content[13:]

                    pl = pcur.execute("SELECT id, privacy FROM playlists WHERE owner = ? AND name = ?", (author, playlist))
                    plist.commit()
                    lenOfPl = 0
                    for row in pl:
                        lenOfPl += 1

                    if lenOfPl != 1: ## There is not such a playlist
                        response = "You don't have such a playlist."
                        return response

                    else:
                        priv = None
                        id = None
                        pl = pcur.execute("SELECT id, privacy FROM playlists WHERE owner = ? AND name = ?", (author, playlist))
                        plist.commit()
                        
                        for row in pl:
                            id = row[0]
                            priv = row[1]

                        if priv == "p":
                            pcur.execute("UPDATE playlists SET privacy = ? WHERE id = ?", ("u", id))
                            plist.commit()
                            
                            response = "Your privacy has changed from public to unpublic."
                        
                        else:
                            pcur.execute("UPDATE playlists SET privacy = ? WHERE id = ?", ("p", id))
                            plist.commit()
                            
                            response = "Your privacy has changed from unpublic to public."

                        return response
            ```
        - If **seven** equals to "favorite", the user's favorite songs should be listed. Favorites songs are selected with `SELECT` command from the **favorites** table in **favorites.db** database, and if there are not any of them, an information response is returned. Otherwise, songs are appended into the `musics` list, and the list is returned.

            ```python
                elif seven == "favorite": ## List favorites of the user
                    ms = fcur.execute("SELECT music FROM favorites WHERE user = ?", (author,))
                    fav.commit()
                    lenOfMs = 0
                    for row in ms:
                        lenOfMs += 1
                    
                    if lenOfMs == 0: ## There is not any favorite songs
                        response = "You don't have any favorite music. Try to add some!!"
                        return response
                    
                    else:
                        musics = ["Favorites"]
                        ms = fcur.execute("SELECT music FROM favorites WHERE user = ?", (author,))
                        fav.commit()
                        for row in ms:
                            musics.append(row[0])
                        
                        return musics
                else: ## Wrong usage
                    return None
            ```

3. ### **Answers**
    > Answers file contains four predetermined answers created via **pyttsx3** library for the `ask` function.
        
    - **ask.mp3:** It is the audio of *"What is your question?"*. It is run when `;;ask -v` command is called.

    - **direct.mp3:** It is the audio of *"Whoops! I need to go to the toilet. Can you check the url that I have sent into the channel?"*. It is run when there is not any quick answer in the `ask` function.

    - **requestErr.mp3:** It is the audio of *"I could not get the request from Google. Can you please type the question again?"*. It is run when there is an error in searching the question in the Google via voice input.

    - **valueErr.mp3:** It is the audio of *"Whoops! There is an issue with your pronunciation. It is OK. You cannot speak as perfect as me, yet you can try to type your question."*. It is run when a voice input is not understood fluently by Ekho.

4. ### **Quotes**
    > Quotes file is created to store auido files of quotes. After quotes has been added to *quotes.db* and converted into **.mp3** files via **pyttsx3** library, their audio files are stored here. To illustrate, **1.mp3** normally stands for the auido file of quote with the ID of 1.

5. ### **Others**
    > There are several other files except above-mentioned ones, which are **requirements.txt** and **.env** files.

    - **requirements.txt:** This file has created to type libraries that I have used in this project.
        > **discord:** This is the main library to create a discord bot.
        
        > **os:** I used this library to take environment variables and create and delete files as well as check whether a file exists or not.

        > **dotenv:** I used `load_dotenv` function from this library to load variables from **.env** file.

        > **sqlite3:** SQLite library for Python. I used this to create and arrange databases.

        > **pyttsx3:** I used this to give voice outputs, and create audio files.

        > **ffmpeg:** This is necessary to determine source of a audio file and play it in a discord voice channel.
       
        > **bs4:** I used `BeautifulSoup` from this library to take html files of web pages.
        
        > **requests:** I used this library to make requests to Google.
       
        > **speech_recognition:** I used this to take voice inputs via users' microphones.
        
        > **youtube_dl:** This library enables to download videos from Youtube. I used only `YoutubeDL` function from this library.
       
        > **pyaudio:** This is a library for audio inputs and outputs.
       
        > **discord.<area>py[voice]:** This needs to be installed in order to play audio files in discord voice channel.

    - **.env:** This file has created to hide the *token* of the EkhoBot, so it is not in the project folder. Thanks to tokens of bots, it can be determined which bot should use the project codes. However, it is important to hide it since if one has access to token of a bot, one can make whatever one wants to do to the bot. Therefore, I hided the token in a environment file and called it by using **os** and **dotenv** libraries.

6. ### **References**
    - Max A's video to use `YoutubeDL`: https://www.youtube.com/watch?v=jHZlvRr9KxM&t=280s
    - `discord.py` documentation: https://discordpy.readthedocs.io/en/stable/index.html
    - `os` documentation: https://docs.python.org/3/library/os.html
    - `dotenv` documentation: https://github.com/theskumar/python-dotenv
    - `sqlite3` documentation: https://docs.python.org/3/library/sqlite3.html
    - `pyttsx3` documentation: https://github.com/nateshmbhat/pyttsx3
    - `ffmpeg` documentation: https://github.com/kkroening/ffmpeg-python
    - `bs4` documentation: https://www.crummy.com/software/BeautifulSoup/bs4/doc/
    - `requests` documentation: https://docs.python-requests.org/en/master/
    - `speech_recognition` documentation: https://github.com/Uberi/speech_recognition
    - `lxml` documentation: https://lxml.de/
    - Special thanks to [Stack Overflow](https://stackoverflow.com/) users:
        - [Viktor Mashkov](https://stackoverflow.com/a/63074741)
        - [Mark Longair](https://stackoverflow.com/a/5815888)
        - [Keyur Potdar](https://stackoverflow.com/a/49269813)
    - GeeksforGeeks guides:
        - https://www.geeksforgeeks.org/python-convert-speech-to-text-and-text-to-speech/
        - https://www.geeksforgeeks.org/how-to-save-pyttsx3-results-to-mp3-or-wav-file/    