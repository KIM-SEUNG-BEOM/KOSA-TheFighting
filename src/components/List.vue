<script>
import { ref, computed } from 'vue';
import InfiniteLoading from 'v3-infinite-loading';
import 'v3-infinite-loading/lib/style.css';

export default {
  components: {
    InfiniteLoading,
  },
  setup() {
    const allChannels = ref([]);
    const page = ref(1);
    const sortOption = ref('popularity'); // Default sorting by popularity

    // Generate and load dummy data
    const loadAllChannels = async ($state) => {
      const dummyData = Array.from({ length: 10 }, (_, i) => ({
        id: page.value * 10 + i,
        name: `Channel ${page.value * 10 + i}`,
        viewers: Math.floor(Math.random() * 1000) + 1,
        thumbnail: 'https://via.placeholder.com/150',
        description: 'This is a dummy description for testing infinite scroll.',
        createdAt: new Date(Date.now() - Math.random() * 100000000), // Random date for sorting by recency
      }));

      if (page.value > 20) {
        $state.complete();
      } else {
        allChannels.value.push(...dummyData);
        $state.loaded();
        page.value++;
      }
    };

    // Computed property for sorting channels based on selected option
    const sortedChannels = computed(() => {
      return [...allChannels.value].sort((a, b) => {
        if (sortOption.value === 'popularity') {
          return b.viewers - a.viewers; // Sort by viewers descending
        } else if (sortOption.value === 'recency') {
          return b.createdAt - a.createdAt; // Sort by creation date descending
        }
      });
    });

    // Function to change sorting option
    const changeSortOption = (option) => {
      sortOption.value = option;
    };

    return {
      allChannels,
      loadAllChannels,
      sortedChannels,
      sortOption,
      changeSortOption,
    };
  },
};
</script>

<template>
  <div class="main-page">
    <!-- Header -->
    <header class="main-header">
      <div class="logo">
        <img src="@/assets/Inplayer.png" alt="Logo" />
      </div>
      <nav>
        <button @click="login">로그인</button>
        <button @click="startBroadcast">방송하기</button>
      </nav>
    </header>

    <!-- Main Container -->
    <div class="main-container">
      <!-- Sidebar -->
      <aside class="sidebar">
        <button @click="goToAllChannels">전체 방송 목록</button>
        <button @click="goToCategory">카테고리</button>
        <button @click="goToFollowing">팔로잉</button>
        <button @click="goToRecommendedStreamers">추천 스트리머 목록</button>
        <button @click="goToNotices">공지사항 게시판</button>
      </aside>

      <!-- Main Content -->
      <main class="content">

        <section class="all-channels">
          <h2>전체 방송 목록</h2>

          <!-- Sorting Options -->
          <div class="sort-options">
            <button :class="{ active: sortOption === 'popularity' }" @click="changeSortOption('popularity')">인기순
            </button>
            <button :class="{ active: sortOption === 'recency' }" @click="changeSortOption('recency')">최신순</button>
          </div>

          <!-- Channel List -->
          <div class="video-list">
            <div v-for="channel in sortedChannels" :key="channel.id" class="channel-card">
              <img :src="channel.thumbnail" alt="Channel thumbnail"/>
              <h3>{{ channel.name }}</h3>
              <span>{{ channel.viewers }} viewers</span>
              <p>{{ channel.description }}</p>
            </div>
          </div>
          <InfiniteLoading @infinite="loadAllChannels"/>
        </section>
      </main>
    </div>

    <!-- Footer -->
    <footer class="main-footer">
      Footer content here
    </footer>
  </div>
</template>

<style scoped>
.main-page {
  display: flex;
  flex-direction: column;
  height: 100vh;
  font-family: Arial, sans-serif;
  color: #fff;
  background-color: #1c1c1c;
  overflow-y: auto;
}

.main-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 20px;
  background-color: #2d2d2d;
}

.main-header .logo {
  font-size: 24px;
  color: #f0a500;
  cursor: pointer;
}

.main-header nav button {
  margin-left: 10px;
  padding: 8px 12px;
  background-color: #f0a500;
  color: #fff;
  border: none;
  cursor: pointer;
}

.main-container {
  display: flex;
  flex: 1;
}

.sidebar {
  width: 200px;
  background-color: #333;
  padding: 20px;
}

.sidebar button,
.sidebar h3 {
  width: 100%;
  margin-bottom: 10px;
  padding: 10px;
  color: #f0a500;
  background-color: #444;
  border: none;
  text-align: left;
  cursor: pointer;
}

.content {
  flex: 1;
  padding: 20px;
  background-color: #2d2d2d;
  overflow-y: auto;
}

.all-channels {
  margin-top: 20px;
}

.sort-options {
  display: flex;
  gap: 10px;
  margin-bottom: 15px;
}

.sort-options button {
  padding: 8px 12px;
  background-color: #444;
  color: #f0a500;
  border: none;
  cursor: pointer;
}

.sort-options button.active {
  background-color: #f0a500;
  color: #2d2d2d;
}

.video-list {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.channel-card {
  display: inline-block;
  width: 200px;
  margin: 10px;
  background-color: #444;
  border-radius: 10px;
  overflow: hidden;
  text-align: center;
}

.channel-card img {
  width: 100%;
  height: auto;
}

.channel-card h3 {
  font-size: 16px;
  margin: 10px 0;
  color: #f0a500;
}

.channel-card p, .channel-card span {
  color: #bbb;
}

.main-footer {
  background-color: #2d2d2d;
  padding: 10px;
  text-align: center;
  color: #fff;
}
</style>