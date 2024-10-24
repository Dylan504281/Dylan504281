import discord
import random
import asyncio
import requests  # Import requests library to fetch dog memes

from discord.ext import commands

# Intents for message content and member updates
intents = discord.Intents.default()
intents.message_content = True
intents.members = True

# Create the Discord client
client = discord.Client(intents=intents)

# Define rarities for dog memes (common, uncommon, rare, epic)
rarities = ["common", "uncommon", "rare", "epic"]
rarity_weights = [0.7, 0.2, 0.07, 0.03]  # Weights for random rarity selection

# Function to get a random dog meme URL
async def get_dog_meme():
    subreddit = "dogmemes"  # You can change this to a different dog meme subreddit
    url = f"https://reddit.com/r/{subreddit}/random/.json"
    async with requests.get(url, headers={"User-Agent": "discord-dog-meme-bot"}) as response:
        if response.status_code == 200:
            data = await response.json()
            return data[0]["data"]["children"][0]["data"]["url"]
        else:
            print(f"Error fetching dog meme: {response.status_code}")
            return None

# Function to determine meme rarity
def get_meme_rarity():
    return random.choices(rarities, weights=rarity_weights)[0]

# Command handler
@client.event
async def on_message(message):
    if message.author == client.user:
        return

    if message.content.startswith("$dogmeme"):
        dog_meme_url = await get_dog_meme()
        if dog_meme_url:
            rarity = get_meme_rarity()
            await message.channel.send(f"Here's a {rarity} dog meme for you! \n{dog_meme_url}")
        else:
            await message.channel.send("Sorry, couldn't find a dog meme at this time.")

# ... (other bot functionalities)

client.run('your_bot_token')
