const fs = require('fs');
const path = require('path');

const DB_DIR = path.join(__dirname);
const STATS_FILE = path.join(DB_DIR, 'stats.json');
const USERS_FILE = path.join(DB_DIR, 'users.json');

// Initialize database files if they don't exist
function initDB() {
  if (!fs.existsSync(STATS_FILE)) {
    const defaultStats = {
      obfuscations: 0,
      dms: 0,
      servers: 0,
      startTime: Date.now(),
      totalUptime: 0,
      sessions: []
    };
    fs.writeFileSync(STATS_FILE, JSON.stringify(defaultStats, null, 2));
  } else {
    // Ensure new fields exist in existing stats
    const stats = JSON.parse(fs.readFileSync(STATS_FILE, 'utf8'));
    let needsUpdate = false;
    
    if (stats.totalUptime === undefined) {
      stats.totalUptime = 0;
      needsUpdate = true;
    }
    if (stats.sessions === undefined) {
      stats.sessions = [];
      needsUpdate = true;
    }
    if (stats.scriptsObfuscated === undefined) {
      stats.scriptsObfuscated = 0;
      needsUpdate = true;
    }
    
    if (needsUpdate) {
      fs.writeFileSync(STATS_FILE, JSON.stringify(stats, null, 2));
    }
  }
  
  if (!fs.existsSync(USERS_FILE)) {
    const defaultUsers = {
      users: []
    };
    fs.writeFileSync(USERS_FILE, JSON.stringify(defaultUsers, null, 2));
  }
}

// Get current stats
function getStats() {
  initDB();
  const data = fs.readFileSync(STATS_FILE, 'utf8');
  return JSON.parse(data);
}

// Get current users
function getUsers() {
  initDB();
  const data = fs.readFileSync(USERS_FILE, 'utf8');
  return JSON.parse(data);
}

// Save stats
function saveStats(stats) {
  fs.writeFileSync(STATS_FILE, JSON.stringify(stats, null, 2));
}

// Save users
function saveUsers(users) {
  fs.writeFileSync(USERS_FILE, JSON.stringify(users, null, 2));
}

// Record an obfuscation
function recordObfuscation(userId, isDM) {
  const stats = getStats();
  const users = getUsers();
  
  // Increment obfuscation count
  stats.obfuscations++;
  stats.scriptsObfuscated = (stats.scriptsObfuscated || 0) + 1;
  
  // Track DM vs Server
  if (isDM) {
    stats.dms++;
  } else {
    stats.servers++;
  }
  
  // Check if user is new
  if (!users.users.includes(userId)) {
    users.users.push(userId);
  }
  
  saveStats(stats);
  saveUsers(users);
}

// Start a new session (called when bot starts)
function startSession() {
  const stats = getStats();
  const now = Date.now();
  
  // If there's an existing session, calculate its duration and add to total
  if (stats.currentSessionStart) {
    const sessionDuration = now - stats.currentSessionStart;
    stats.totalUptime = (stats.totalUptime || 0) + sessionDuration;
    
    // Add to sessions array
    stats.sessions = stats.sessions || [];
    stats.sessions.push({
      start: stats.currentSessionStart,
      end: now,
      duration: sessionDuration
    });
  }
  
  // Start new session
  stats.startTime = now;
  stats.currentSessionStart = now;
  
  saveStats(stats);
  console.log(`[Database] New session started at ${new Date(now).toISOString()}`);
  return stats;
}

// Get current session uptime
function getCurrentSessionUptime() {
  const stats = getStats();
  if (!stats.currentSessionStart) return 0;
  return Date.now() - stats.currentSessionStart;
}

// Get total uptime (current session + previous sessions)
function getTotalUptime() {
  const stats = getStats();
  const currentSession = getCurrentSessionUptime();
  return (stats.totalUptime || 0) + currentSession;
}

// Format uptime in a readable format
function formatUptime(ms) {
  const days = Math.floor(ms / (1000 * 60 * 60 * 24));
  const hours = Math.floor((ms % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
  const minutes = Math.floor((ms % (1000 * 60 * 60)) / (1000 * 60));
  const seconds = Math.floor((ms % (1000 * 60)) / 1000);
  
  let uptime = '';
  if (days > 0) uptime += `${days}d `;
  if (hours > 0 || days > 0) uptime += `${hours}h `;
  if (minutes > 0 || hours > 0 || days > 0) uptime += `${minutes}m `;
  uptime += `${seconds}s`;
  
  return uptime.trim();
}

// Get uptime - returns formatted string with both current session and total
function getUptime(startTime) {
  const currentSession = getCurrentSessionUptime();
  const total = getTotalUptime();
  
  return {
    current: formatUptime(currentSession),
    total: formatUptime(total)
  };
}

// Get unique user count
function getUniqueUserCount() {
  const users = getUsers();
  return users.users.length;
}

// Get scripts obfuscated count
function getScriptsObfuscatedCount() {
  const stats = getStats();
  return stats.scriptsObfuscated || stats.obfuscations || 0;
}

// Initialize on load
initDB();

module.exports = {
  getStats,
  getUsers,
  recordObfuscation,
  getUptime,
  getUniqueUserCount,
  startSession,
  getCurrentSessionUptime,
  getTotalUptime,
  formatUptime,
  getScriptsObfuscatedCount
};

