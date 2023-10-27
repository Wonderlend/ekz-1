САМОЕ НАЧАЛО БЕЗ ВЫДЕЛЕНИЯ ПОСЛЕДНЕЙ

from aiogram import Bot, types
from aiogram.dispatcher import Dispatcher
from aiogram.utils import executor
from converter import Converter
from pathlib import Path
import soundfile
import os


-------------------------------------------------------------------------------------------
ПОСЛЕ ДВУХ СТРОК 

async def handle_file(file: types.File, file_name: str, path: str):
    Path(f"{path}").mkdir(parents=True, exist_ok=True)
    await bot.download_file(file_path=file.file_path, destination=f"{path}/{file_name}")
    
    
--------------------------------------------------------------------------------------------
ПОСЛЕ ПОСЛЕДНЕГО @dp ДО if name 


@dp.message_handler(content_types=[types.ContentType.VOICE])
async def echo_message(msg: types.Message):
    voice = await msg.voice.get_file()
    path = "/home/ubuntu/demo_bot"
    await handle_file(file=voice, file_name=f"{voice.file_id}.ogg", path=path)
    full_path = f"{path}/{voice.file_id}.ogg"
    data, samplerate = soundfile.read(full_path)
    soundfile.write(f"{full_path}.wav", data, samplerate)
    converter = Converter(f"{full_path}.wav")
    message_text = converter.audio_to_text()
    os.remove(full_path)
    del converter
    await bot.send_message(msg.from_user.id, message_text)
    
-------------------------------------------------------------------------
converter.py

import speech_recognition as sr
import os


class Converter:

    def __init__(self, path_to_file: str, language: str = "ru-RU"):
        self.language = language
        self.wav_file = path_to_file

    def audio_to_text(self) -> str:
        r = sr.Recognizer()

        with sr.AudioFile(self.wav_file) as source:
            audio = r.record(source)
            r.adjust_for_ambient_noise(source)

        return r.recognize_google(audio, language=self.language)

    def __del__(self):
        os.remove(self.wav_file)

