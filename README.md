# monenobot<br>
萌音のbotOSS<br>
<br>
# 主な機能<br>
Welcomeメッセージ<br>
Leaveメッセージ<br>
TicketTooL<br>
VC移動<br>
ピン止め<br>
ロールパネル<br>
VC作成<br>
ダイスロール<br>
ユーザー情報<br>
botの情報<br>
経済<br>
Kick/Ban<br>
<br>
# 参考までにMySQLのテーブル類
GRANT ALL PRIVILEGES ON * . * TO 'botuser03'@'%';　<br>
<br>
use discord_mone03　<br>
Database changed　<br>
CREATE TABLE temp_vc_channels (　<br>
         guild_id BIGINT PRIMARY KEY,　<br>
         parent_channel_id BIGINT UNIQUE NOT NULL　<br>
    );　<br>
　<br>
CREATE TABLE owned_vc_channels (　<br>
         vc_channel_id BIGINT PRIMARY KEY,　<br>
         owner_id BIGINT NOT NULL　<br>
     );　<br>
　<br>
 -- Welcome設定テーブル　<br>
 CREATE TABLE IF NOT EXISTS welcome_settings (　<br>
         id INT AUTO_INCREMENT PRIMARY KEY,　<br>
         guild_id BIGINT NOT NULL,　<br>
         channel_id BIGINT NOT NULL,　<br>
         message TEXT NOT NULL,　<br>
         role_id BIGINT NULL,　<br>
         created_at DATETIME NOT NULL,　<br>
         deleted_at DATETIME NULL,　<br>
         INDEX idx_welcome_guild (guild_id)　<br>
     ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;　<br>

-- Welcomeログテーブル
 CREATE TABLE IF NOT EXISTS welcome_logs (　<br>
         id INT AUTO_INCREMENT PRIMARY KEY,　<br>
         guild_id BIGINT NOT NULL,　<br>
         member_id BIGINT NOT NULL,　<br>
         joined_at DATETIME NOT NULL,　<br>
         member_count INT NOT NULL,　<br>
         INDEX idx_welcome_logs_guild (guild_id),　<br>
         INDEX idx_welcome_logs_member (member_id)　<br>
     ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;　<br>
<br>
-- チケット設定　<br>
 CREATE TABLE IF NOT EXISTS ticket_settings (　<br>
         id INT AUTO_INCREMENT PRIMARY KEY,　<br>
         guild_id BIGINT NOT NULL,　<br>
         category_id BIGINT NOT NULL,　<br>
         support_role_id BIGINT NOT NULL,　<br>
         created_at DATETIME NOT NULL,　<br>
         deleted_at DATETIME NULL,　<br>
         INDEX idx_ticket_settings_guild (guild_id)　<br>
     ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;　<br>
<br>
<br>
-- チケット本体　<br>
CREATE TABLE IF NOT EXISTS tickets (　<br>
         id INT AUTO_INCREMENT PRIMARY KEY,　<br>
         guild_id BIGINT NOT NULL,　<br>
         channel_id BIGINT NOT NULL,　<br>
         user_id BIGINT NOT NULL,　<br>
         status ENUM('open','closed') DEFAULT 'open',　<br>
         created_at DATETIME NOT NULL,　<br>
         closed_at DATETIME NULL,　<br>
         INDEX idx_tickets_guild (guild_id),　<br>
         INDEX idx_tickets_user (user_id),　<br>
         INDEX idx_tickets_channel (channel_id)　<br>
     ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;　<br>
<br>
 -- チケットログ（アクション履歴）　<br>
CREATE TABLE IF NOT EXISTS ticket_logs (　<br>
         id INT AUTO_INCREMENT PRIMARY KEY,　<br>
         ticket_id INT NOT NULL,　<br>
         action VARCHAR(255) NOT NULL,　<br>
         user_id BIGINT NOT NULL,　<br>
         timestamp DATETIME NOT NULL,　<br>
         FOREIGN KEY (ticket_id) REFERENCES tickets(id) ON DELETE CASCADE,　<br>
         INDEX idx_ticket_logs_ticket (ticket_id)　<br>
     ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;　<br>
<br>
CREATE TABLE IF NOT EXISTS pinned_messages (　<br>
         message_id BIGINT PRIMARY KEY,　<br>
         guild_id BIGINT NOT NULL,　<br>
         channel_id BIGINT NOT NULL,　<br>
         author_id BIGINT NOT NULL,　<br>
         pinned_by_id BIGINT NOT NULL,　<br>
         content TEXT NOT NULL,　<br>
         created_at DATETIME NOT NULL　<br>
     );　<br>
 CREATE TABLE IF NOT EXISTS leave_logs (　<br>
         id INT AUTO_INCREMENT PRIMARY KEY,　<br>
         guild_id BIGINT NOT NULL,　<br>
         member_id BIGINT NOT NULL,　<br>
         left_at DATETIME NOT NULL,　<br>
         member_count INT NOT NULL　<br>
     );　<br>
　<br>
CREATE TABLE IF NOT EXISTS leave_settings (　<br>
         id INT AUTO_INCREMENT PRIMARY KEY,　<br>
         guild_id BIGINT NOT NULL,　<br>
         channel_id BIGINT NOT NULL,　<br>
         message TEXT NOT NULL,　<br>
         created_at DATETIME NOT NULL,　<br>
         deleted_at DATETIME DEFAULT NULL　<br>
     );　<br>
<br>
CREATE INDEX idx_leave_settings_guild ON leave_settings(guild_id);　<br>
CREATE INDEX idx_leave_logs_guild ON leave_logs(guild_id);　<br>
CREATE INDEX idx_leave_logs_member ON leave_logs(member_id);　<br>
