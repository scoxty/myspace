<template>
    <ContentBase>
        <div class="row">
            <div class="col-3">
                <UserDynamicsInfo @follow="follow" @unfollow="unfollow" :user="user" :is_me="is_me" />
                <UserDynamicsWrite v-if="is_me" @post_a_post="post_a_post" />
            </div>
            <div class="col-9">
                <UserDynamicsPost :user="user" :posts="posts" @delete_a_post="delete_a_post" />
            </div>
        </div>
    </ContentBase>
</template>
  
<script>
import ContentBase from '../components/ContentBase.vue';
import UserDynamicsInfo from '../components/UserDynamicsInfo.vue';
import UserDynamicsPost from '../components/UserDynamicsPost.vue';
import UserDynamicsWrite from '../components/UserDynamicsWrite.vue';
import { reactive } from 'vue';
import { useRoute } from 'vue-router';
import $ from 'jquery';
import { useStore } from 'vuex';
import { computed } from 'vue';


export default {
    name: 'UserDynamics',
    components: {
        ContentBase,
        UserDynamicsInfo,
        UserDynamicsPost,
        UserDynamicsWrite,
    },
    setup() {
        const store = useStore();
        const route = useRoute();
        const userId = parseInt(route.params.userId);

        const user = reactive({
        });
        const posts = reactive({
        });

        $.ajax({
            url: "https://app165.acapp.acwing.com.cn/myspace/getinfo/",
            type: "GET",
            data: {
                user_id: userId,
            },
            headers: {
                'Authorization': "Bearer " + store.state.user.access,
            },
            success(resp) {
                user.id = resp.id;
                user.username = resp.username;
                user.photo = resp.photo;
                user.followerCount = Math.max(resp.followerCount, 0);
                user.is_followed = resp.is_followed;
            },
        })

        $.ajax({
            url: "https://app165.acapp.acwing.com.cn/myspace/post/",
            type: "GET",
            data: {
                user_id: userId,
            },
            headers: {
                'Authorization': "Bearer " + store.state.user.access,
            },
            success(resp) {
                posts.count = resp.length;
                posts.posts = resp;
            }
        })

        const follow = () => {
            if (user.is_followed)
                return;
            user.is_followed = true;
            user.followerCount++;
        };

        const unfollow = () => {
            if (!user.is_followed)
                return;
            user.is_followed = false;
            user.followerCount--;
        };

        const post_a_post = (content) => {
            posts.posts.unshift({
                id: posts.count,
                userId: user.id,
                content: content,
            });
        };

        const delete_a_post = (post_id) => {
            posts.posts = posts.posts.filter(post => post.id !== post_id);
            posts.count = posts.posts.length;
        }

        const is_me = computed(() => userId === store.state.user.id);

        return {
            user,
            follow,
            unfollow,
            posts,
            post_a_post,
            delete_a_post,
            is_me,
        }
    }
}
</script>
  
<style scoped></style>

  