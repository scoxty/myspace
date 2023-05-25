<template>
    <ContentBase>
        <div class="card" v-for="user in users" :key="user.id" @click="open_user_dynamics(user.id)">
            <div class="card-body">
                <div class="row">
                    <div class="col-1 img-field">
                        <img class="img-fluid" :src="user.photo" alt="头像">
                    </div>
                    <div class="col-11">
                        <div class="username">{{ user.username }}</div>
                        <div class="followerCount">粉丝：{{ user.followerCount }}</div>
                    </div>
                </div>
            </div>
        </div>
    </ContentBase>
</template>
  
<script>
import ContentBase from '../components/ContentBase.vue';
import $ from 'jquery';
import { ref } from 'vue';
import { useStore } from 'vuex';
import router from '@/router/index.js';

export default {
    name: 'UserList',
    components: {
        ContentBase,
    },
    setup() {
        let users = ref([]);
        const store = useStore();

        $.ajax({
            url: 'https://app165.acapp.acwing.com.cn/myspace/userlist/',
            type: "get",
            success(resp) {
                users.value = resp;
            }
        });

        const open_user_dynamics = userId => {
            if (store.state.user.is_login) {
                router.push({
                    name: "userdynamics",
                    params: {
                        userId
                    }
                })
            } else {
                router.push({
                    name: "login"
                })
            }
        }

        return {
            users,
            open_user_dynamics,
        };
    }
}
</script>
  
<style scoped>
.img-fluid {
    border-radius: 50%;
}

.username {
    font-weight: bolder;
    height: 50%;
}

.followerCount {
    font-size: 12px;
    color: grey;
    height: 50%;
}

.card {
    margin-bottom: 20px;
    cursor: pointer;
}

.card:hover {
    box-shadow: 2px 2px 10px grey;
    transition: 500ms;
}

.img-field {
    display: flex;
    flex-direction: column;
    justify-content: center;
}
</style>