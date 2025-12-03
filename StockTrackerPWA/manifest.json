const CACHE_NAME = 'stock-tracker-v1';
const urlsToCache = [
  'stock_tracker.html', // 您的主頁面
  'manifest.json',      // PWA 設定檔
  // 如果有其他外部CSS或JS，請加在這裡
];

// 1. 安裝階段：快取所有核心資源
self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then((cache) => {
        console.log('Opened cache and added URLs');
        return cache.addAll(urlsToCache);
      })
  );
  self.skipWaiting(); // 強制新的 Service Worker 立即啟用
});

// 2. 獲取階段：從快取中提供資源，如果失敗則嘗試網路
self.addEventListener('fetch', (event) => {
  // 排除 Firebase 連線，確保 Firestore 連線走網路
  if (event.request.url.includes('firebase') || event.request.url.includes('googleapis')) {
    return;
  }
  
  event.respondWith(
    caches.match(event.request)
      .then((response) => {
        // Cache hit - return response
        if (response) {
          return response;
        }
        // No cache match - fetch from network
        return fetch(event.request);
      })
  );
});

// 3. 啟用階段：清除舊版本的快取
self.addEventListener('activate', (event) => {
  const cacheWhitelist = [CACHE_NAME];
  event.waitUntil(
    caches.keys().then((cacheNames) => {
      return Promise.all(
        cacheNames.map((cacheName) => {
          if (cacheWhitelist.indexOf(cacheName) === -1) {
            return caches.delete(cacheName);
          }
        })
      );
    })
  );
  self.clients.claim();
});