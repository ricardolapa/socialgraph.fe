<template>
    <div class="content">
        <h3>Select a user from the drop.</h3>
        
        <!-- List of Users -->
        <select @change="onSelectUser($event)">
            <option>-- Select --</option>
            <option v-for="user in users" :key="user.id" :value="user.id">
                {{ user.firstname }} {{ user.surname }}
            </option>
        </select>

        <div class="md-layout md-gutter" v-if="notEmptyObject(user)">
            <div class="md-layout-item ">
                <h4>Details</h4>

                <h1>{{ user.firstname }} {{ user.surname }}</h1>
                <hr>
                <p>Age: <strong>{{ user.age }}</strong></p>
                <p>Gender: <strong>{{ user.gender }}</strong></p>
                <p>Number of Connections: <strong>{{ user.conections.length }}</strong></p>
                <p>Number of Visited Cities: <strong>{{ user.cities.length }}</strong></p>

                <div class="md-layout md-gutter">
                    <div class="md-layout-item md-size-50">
                        <h4 class="subtitle">Sugested Trips</h4>
                        <div v-if="notEmptyObject(tripsRecomended)">
                            <md-card v-for="(item, index) in tripsRecomended" :key="index">
                                <md-card-header>
                                    <div class="md-title">{{ item.name }} </div>
                                </md-card-header>

                                <md-card-content>
                                    <p>Times Visited: <strong>{{ item.times_visited }}</strong></p>
                                    <p>Avarage Rate: <strong>{{ item.avarage_rate }}</strong></p>
                                </md-card-content>

                            </md-card>
                        </div>
                    </div>

                    <div class="md-layout-item md-size-25">
                        <h4 class="subtitle">Friends Of Friends</h4>
                        <div v-if="notEmptyObject(friendsOfFriends)">
                    
                            <md-card v-for="(item, index) in friendsOfFriends" :key="index">
                                <md-card-header>
                                    <div class="md-title">{{ item.firstname }} {{ item.surname }}</div>
                                </md-card-header>

                                <md-card-content>
                                    <p>Age: <strong>{{ item.age }}</strong></p>
                                    <p>Gender: <strong>{{ item.gender }}</strong></p>
                                </md-card-content>

                            </md-card>
                        </div>
                    </div>

                    <div class="md-layout-item md-size-25">
                        <h4 class="subtitle">Suggested Friends</h4>
                        <div v-if="notEmptyObject(suggestedFriends)">
                            <p v-if="suggestedFriends.message">{{ suggestedFriends.message }}</p>
                            
                            <div v-else>
                                <md-card v-for="(item, index) in suggestedFriends" :key="index">
                                    <md-card-header>
                                        <div class="md-title">{{ item.firstname }} {{ item.surname }}</div>
                                    </md-card-header>

                                    <md-card-content>
                                        <p>Age: <strong>{{ item.age }}</strong></p>
                                        <p>Gender: <strong>{{ item.gender }}</strong></p>
                                    
                                    </md-card-content>

                                </md-card>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <div class="md-layout-item md-size-25">
                <h4>Friends</h4>
                
                <div v-if="notEmptyObject(friends)">
                    
                     <md-card v-for="(item, index) in friends" :key="index">
                        <md-card-header>
                            <div class="md-title">{{ item.firstname }} {{ item.surname }}</div>
                        </md-card-header>

                        <md-card-content>
                            <p>Age: <strong>{{ item.age }}</strong></p>
                            <p>Gender: <strong>{{ item.gender }}</strong></p>
                            <p>Number of Connections: <strong>{{ item.conections.length }}</strong></p>
                            <p>Number of Visited Cities: <strong>{{ item.trips.length }}</strong></p>
                        </md-card-content>

                    </md-card>
                </div>
            </div>
           
            <div class="md-layout-item md-size-25">
                <h4>Trips</h4>
                <div v-if="notEmptyObject(trips)">
                     <md-card v-for="(item, index) in trips" :key="index">
                        <md-card-header>
                            <div class="md-title">{{ item.city }} {{ item.surname }}</div>
                        </md-card-header>

                        <md-card-content>
                            <p>Rate: <strong>{{ item.rate }}</strong></p>
                        </md-card-content>

                    </md-card>
                </div>
            </div>
        </div>

    </div>
</template>

<script>
import axios from 'axios';

export default {
    name: 'content-component',

    data() {
        return {
            user: {},
            friends: {},
            trips: {},
            tripsRecomended: {},
            friendsOfFriends: {},
            suggestedFriends: {},
            users: []
        }
    },

    methods: {
        fetchAllUsers() {
            axios.get('http://127.0.0.1:8000/person')
                .then(res => this.users = res.data);
        },

        onSelectUser(ev) {
            this.resetVars();
            this.axios.get(`http://127.0.0.1:8000/person-details/${ev.target.value}`)
                .then(res => {
                    this.user = res.data;
                    this.fetchFiendsDetails(this.user.id);
                    this.fetchTripsDetails(this.user.id);
                    this.getRecomendedTrips(this.user.id);
                    this.getFriendsOfFriends(this.user.id);
                    this.getSuggestedFriends(this.user.id)
                });
        },

        fetchFiendsDetails(userId) {
            if (this.user.conections) {
                this.axios.get(`http://127.0.0.1:8000/person-friends/${userId}`)
                    .then(result => this.friends = result.data)
            }
        },

        fetchTripsDetails(userId) {
            if (this.user.conections) {
                this.axios.get(`http://127.0.0.1:8000/person-trips/${userId}`)
                    .then(result => this.trips = result.data)
            }
        },

        getRecomendedTrips(userId) {
            if (this.user.conections) {
                this.axios.get(`http://127.0.0.1:8000/trips-recomended/${userId}`)
                    .then(result => this.tripsRecomended = result.data)
            }
        },

        getFriendsOfFriends(userId) {
            if (this.user.conections) {
                this.axios.get(`http://127.0.0.1:8000/person-friends/of-friends/${userId}`)
                    .then(result => this.friendsOfFriends = result.data)
            }
        },

        getSuggestedFriends(userId) {
            if (this.user.conections) {
                this.axios.get(`http://127.0.0.1:8000/person-friends/suggested-friends/${userId}`)
                    .then(result => this.suggestedFriends = result.data)
            }
        },

        // Helpers
        notEmptyObject(someObject){
            return Object.keys(someObject).length
        },

        resetVars() {
            this.user = {};
            this.friends = {};
            this.trips = {};
            this.tripsRecomended = {};
            this.suggestedFriends = {};
            this.friendsOfFriends = {};
        }
    },

    mounted() {
        this.fetchAllUsers();
    }
    
}
</script>

<style>
    .content {
        width: 100%;
        padding: 2em;
    }
    select {
        margin-bottom: 20px;
    }
    .subtitle {
        margin-top: 40px;
    }
</style>