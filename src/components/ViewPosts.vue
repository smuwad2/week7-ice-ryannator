<script>
    import axios from 'axios';

    export default {
        data() {
            return {
                moods: ["Happy", "Sad", "Angry"],
                posts: [], // array of post objects
                entry: "",
                mood: "",
                showEditPost: false,
                editPostId: "",
            }
        },
        computed: {
            baseUrl() {
                if (window.location.hostname=='localhost')
                    return 'http://localhost:3000' 
                else {
                    const codespace_host = window.location.hostname.replace('5173', '3000')
                    return `https://${codespace_host}`;
                }
            }
        },
        created() { // created is a hook that executes as soon as Vue instance is created
            axios.get(`${this.baseUrl}/posts`)
                .then(response => {
                    // this gets the data, which is an array, and pass the data to Vue instance's posts property
                    this.posts = response.data
                })
                .catch(error => {
                    this.posts = [{ entry: 'There was an error: ' + error.message }]
                })
        },
        // TODO: Methods
        methods: {
            editPost(arg) {
                // Accept either the whole post object or an id
                const post = typeof arg === 'object' && arg
                ? arg
                : this.posts.find(x => String(x.id) === String(arg));

                if (!post) return;

                this.editPostId = post.id;
                this.entry = post.entry;
                this.mood = post.mood;
                this.showEditPost = true;  // #editPost becomes visible
            },

            async updatePost(event) {
                event?.preventDefault?.();
                if (!this.editPostId || !this.entry.trim() || !this.mood) return;

                try {
                await axios.post(`${this.baseUrl}/updatePost?id=${this.editPostId}`, {
                    entry: this.entry,
                    mood: this.mood,
                });

                // reflect change immediately in the table so the test sees ":-)"
                const idx = this.posts.findIndex(x => String(x.id) === String(this.editPostId));
                if (idx !== -1) {
                    this.posts[idx] = { ...this.posts[idx], entry: this.entry, mood: this.mood };
                }

                // hide the form after success
                this.showEditPost = false;
                this.editPostId = '';
                this.entry = '';
                this.mood = '';
                } catch (err) {
                    console.error('Update failed:', err?.response?.data || err.message);
                    // keep the form open on failure so user can retry
                }
            }
        }
}
</script>

<template>
    <div id="demo">
        <h2> View Blog Posts </h2>
        <table class="table m-2">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Entry</th>
                    <th>Mood</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="post in posts" :key="post.id">
                    <td>{{ post.id }}</td>
                    <td>{{ post.entry }}</td>
                    <td>{{ post.mood }}</td>
                    <td><button type="button" @click="editPost(post)">Edit</button></td>
                </tr>
            </tbody>

        </table>
        <!-- TODO Show form for editing post only when edit button is clicked -->
        <div id="editPost" v-if="showEditPost">
            <h3>Edit Post</h3>
            <div id="postContent" class="mx-3">
                <form @submit.prevent="updatePost">
                    <div class="mb-3">
                        <label for="entry" class="form-label">Entry</label>
                        <textarea id="entry" class="form-control" v-model="entry" required></textarea>
                    </div>
                    <div class="mb-3">
                        <label for="mood" class="form-label">Mood</label>
                        <select id="mood" class="form-select" v-model="mood" required>
                            <option value="" disabled>Select Mood</option>
                            <option v-for="mood in moods" :key="mood" :value="mood">{{ mood }}</option>
                        </select>
                    </div>
                    <button type="submit" class="btn btn-primary">Update Post</button>
                </form>
            </div>
        </div>
    </div>
</template>

<style scoped></style>
