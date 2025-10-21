<script>
import axios from 'axios';

    export default { 

        // add code here
        data() {
            return {
                moods: ['Happy', 'Sad', 'Angry'], // dropdown options
                selMood: "",
                subject: "",
                entry: "",
            }
        }, 
        computed: {
            baseUrl() {
                return "http://localhost:3000";
            }
        },
        methods: {
            async addPost() {
                const { data } = await axios.get(`${this.baseUrl}/addPost`, {
                    params: { 
                        subject: this.subject, 
                        entry: this.entry, mood: 
                        this.selMood 
                    }
                }).then(response => {
                    this.outputMsg = response.data.message; 
                }).catch(error => {
                    console.log(error); 
                })
            }
        }
    }
</script>

<template>
    <div class="table m-2">
        <h3>Add a New Blog Post</h3>

        Subject: <input type='text' size='30' v-model='subject' required>
        <br>

        Entry: <br>
        <textarea name='entry' cols='80' rows='5' v-model='entry' required></textarea>
        <br>

        Mood:
        <!-- TODO: Build a dropdown list here for selecting the mood -->
        <select v-model="selMood">
            <option v-for="mood in moods">
                {{ mood }}
            </option>
        </select>

        <br>

        <br>
        <button @click="addPost">Submit New Post</button>
        {{ outputMsg }}
        <hr> Click  <a><router-link to="/ViewPosts/">here</router-link></a>  to return to Main Page
       
    </div>
</template>

