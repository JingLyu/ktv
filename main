###100

import tkinter as tk
from tkinter import filedialog
import subprocess
import threading
import os
import time
import signal  # Import the signal module



class MusicPlayerApp:
    def __init__(self, master):
        self.master = master
        self.master.title("Karaok J Player")

        self.song_list = []
        self.current_song_index = 0
        self.playing = False
        self.process = None

        self.song_listbox = tk.Listbox(master, selectmode=tk.SINGLE, height=30, width=50)
        self.song_listbox.pack(pady=10)

        self.add_button = tk.Button(master, text="Add Song", command=self.add_song)
        self.add_button.pack(pady=5)

        self.play_button = tk.Button(master, text="Play", command=self.play_music)
        self.play_button.pack(pady=5)

        self.next_button = tk.Button(master, text="Next", command=self.play_next_song)
        self.next_button.pack(pady=5)
    

        #self.switch_button = tk.Button(master, text="Switch", command=self.switch_audio_track)
        #self.switch_button.pack(pady=5)
        
        self.close_button = tk.Button(master, text="Close", command=self.close_app)
        self.close_button.pack(pady=5)

        #self.remove_button = tk.Button(master, text="Remove", command=self.remove_song)
        #self.remove_button.pack(pady=5)
        
        self.pause_button = tk.Button(master, text="Pause", command=self.pause_music)
        self.pause_button.pack(pady=5)

        self.add_to_top_button = tk.Button(master, text="Add to Top", command=self.add_to_top_and_play)
        self.add_to_top_button.pack(pady=5)


    def add_to_top_and_play(self):
        file_path = filedialog.askopenfilename(title="Select a song", filetypes=[("All Files", "*.*")])
        if file_path:
            self.song_list.insert(0, file_path)
            self.song_listbox.insert(0, os.path.basename(file_path))


    def pause_music(self):
        if self.process and self.process.poll() is None:
            # If a song is currently playing, pause the subprocess
            self.process.send_signal(signal.SIGSTOP)  # Send the STOP signal to pause
            self.playing = False
            self.pause_button.config(text="Resume", command=self.resume_music)

    def resume_music(self):
        # Resume playback from the paused state
        if not self.playing:
            self.process.send_signal(signal.SIGCONT)  # Send the CONT signal to resume
            self.playing = True
            self.pause_button.config(text="Pause", command=self.pause_music)

        

    def remove_song(self):
        selected_index = self.song_listbox.curselection()
        if selected_index:
            # Get the index of the selected item and remove it from the list
            index_to_remove = selected_index[0]
            self.song_list.pop(index_to_remove)

            # Remove the selected item from the Listbox
            self.song_listbox.delete(index_to_remove)        

    def add_song(self):
        file_paths = filedialog.askopenfilenames(title="Select songs", filetypes=[("All Files", "*.*")])
        if file_paths:
            self.song_list.extend(file_paths)
            for file_path in file_paths:
                self.song_listbox.insert(tk.END, os.path.basename(file_path))

    def play_music(self):
        if not self.playing and self.song_list:
            selected_index = self.song_listbox.curselection()
            if selected_index:
            # Get the index of the selected item and remove it from the list
                self.current_song_index = selected_index[0]


            self.playing = True
            self.play_next_song()

    def play_next_song(self):
        if self.song_list and self.current_song_index < len(self.song_list):
            if self.process and self.process.poll() is None:
                # If a song is already playing, terminate the subprocess
                self.process.terminate()
            
            
            

            audio_track_number = 1     
            #vlc_args = ['--fullscreen','--play-and-exit', '--extraintf', 'rc', '--audio-track', str(audio_track_number)]  # Use str() to convert audio_track_number to a string
            vlc_args = ['--fullscreen','--play-and-exit', '--audio-track', str(audio_track_number)]  # Use str() to convert audio_track_number to a string

            song_to_play = self.song_list[self.current_song_index]
            
            
            self.process = subprocess.Popen(['/Applications/VLC.app/Contents/MacOS/VLC'] + [song_to_play] + vlc_args)

            # Start a separate thread to wait for the song to finish playing
            threading.Thread(target=self.wait_for_song_completion).start()
            
            self.song_listbox.selection_clear(0, tk.END)  # Clear any existing selection
            self.song_listbox.selection_set(self.current_song_index)  # Set the selection at the specified index



            self.remove_song()
            # Move to the next song in the list

            #self.current_song_index += 1
            print ('after thread add index')
            #self.master.after(2000, self.play_next_song)





    def wait_for_song_completion(self):
        while self.process.poll() is None:
            time.sleep(0.3)  # Short delay to avoid busy-waiting
        #self.process.wait()
        #self.playing = False

        # If there are more songs in the list, play the next one
        if self.playing and self.current_song_index < len(self.song_list):
            #remove previous played song


            
            self.play_next_song()

    def switch_audio_track(self):
        # Check if a song is currently playing and there are multiple audio tracks
        if self.process and self.process.poll() is None:
            song_to_play = self.song_list[self.current_song_index]
            audio_track_number = self.get_next_audio_track()

            switch_audio_track(song_to_play, audio_track_number)
           


 
    def get_next_audio_track(self):
        # Simple logic to switch between audio tracks (1, 2, 3, ...)
        return (self.current_song_index % 3) + 1


    def close_app(self):
        # Function associated with the Close button
        # Destroy the Tkinter window
        self.master.destroy()
    
if __name__ == "__main__":
    root = tk.Tk()
    root.geometry("1200x900")

    app = MusicPlayerApp(root)
    root.mainloop()
