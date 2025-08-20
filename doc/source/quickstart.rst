from kivy.properties import ObjectProperty ,StringProperty,BooleanProperty,NumericProperty,DictProperty
from kivy.lang import Builder
from kivy.base import EventLoop
from kivy.metrics import dp as sdp
from kivy.clock import Clock,mainthread
from kivy.core.window import Window

from kivymd.app import MDApp
from kivymd.uix.screen import MDScreen
from kivymd.uix.label import MDLabel
#from kivymd.uix.dialog import BaseDialog
from functools import partial
import os

import threading

from chatbot import Chat
#import wikipedia
import asynckivy
dirname, filename = os.path.split(os.path.abspath(__file__))
Builder.load_file(os.path.join(dirname,'ui2.kv'))

#For more information about Chatbot 
#https://github.com/ahmadfaizalbh/Chatbot
# @register_call("whoIs")
#This can also be used 
#you have to add in buildozer 
# def who_is(session, query):
#     try:
#         return wikipedia.summary(query)
#     except Exception:
#         for new_query in wikipedia.search(query):
#             try:
#                 return wikipedia.summary(new_query)
#             except Exception:
#                 pass
#     return "I don't know about "+query

# first_question="Hi, how are you?"

chatbotai=Chat()
#.converse(first_question,gui=True)


class UI2ChatLabel(MDLabel):
    sender=BooleanProperty(True)
    def on_kv_post(self,obj):
        if self.sender:
            self.pos_hint={'right':1}
        else:
            self.pos_hint={'x':0}


class UI2Screen(MDScreen):
    obj=ObjectProperty(None)
    _bot_state=BooleanProperty(False)
    def on_enter(self):
        Window.softinput_mode='below_target'
    
    def on_leave(self):
        Window.softinput_mode=''

    def _send_message(self):
    

        _in=self.ids.ti.text
        print(_in)
        

        self.ids.chat_box.add_widget(UI2ChatLabel(text=_in,sender=True))
        self._bot_state=True
        threading.Thread(target=partial(self.get_data,_in)).start()
        self.ids.ti.text=''
        
        
        
    def get_data(self,_in):
        _ou=chatbotai.respond(_in)
        self._bot_state=False
        self.set_data(_ou)

    @mainthread
    def set_data(self,out):

        self.ids.chat_box.add_widget(UI2ChatLabel(text=out,sender=False))
    def _back_press(self):
        self.obj.go_back_mainscreen()





class MainApp(MDApp):
    def build(self):
        
        return UI2Screen(obj=self)
    def on_start(self):
        super().on_start()
        #self.fps_monitor_start()
if __name__=='__main__':
    MainApp().run()
