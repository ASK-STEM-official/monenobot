# monenobot
萌音のbotOSS

# 主な機能
Welcomeメッセージ
Leaveメッセージ
TicketTooL
VC移動
ピン止め
ロールパネル
VC作成
ダイスロール
ユーザー情報
botの情報
経済
Kick/Ban

# 参考までにMySQLのテーブル類
mysql> GRANT ALL PRIVILEGES ON * . * TO 'botuser03'@'%';
Query OK, 0 rows affected (0.01 sec)

mysql> use discord_mone03
Database changed
CREATE TABLE temp_vc_channels (
         guild_id BIGINT PRIMARY KEY,
         parent_channel_id BIGINT UNIQUE NOT NULL
    );

CREATE TABLE owned_vc_channels (
         vc_channel_id BIGINT PRIMARY KEY,
         owner_id BIGINT NOT NULL
     );

 -- Welcome設定テーブル
 CREATE TABLE IF NOT EXISTS welcome_settings (
         id INT AUTO_INCREMENT PRIMARY KEY,
         guild_id BIGINT NOT NULL,
         channel_id BIGINT NOT NULL,
         message TEXT NOT NULL,
         role_id BIGINT NULL,
         created_at DATETIME NOT NULL,
         deleted_at DATETIME NULL,
         INDEX idx_welcome_guild (guild_id)
     ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Welcomeログテーブル
 CREATE TABLE IF NOT EXISTS welcome_logs (
         id INT AUTO_INCREMENT PRIMARY KEY,
         guild_id BIGINT NOT NULL,
         member_id BIGINT NOT NULL,
         joined_at DATETIME NOT NULL,
         member_count INT NOT NULL,
         INDEX idx_welcome_logs_guild (guild_id),
         INDEX idx_welcome_logs_member (member_id)
     ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- チケット設定
 CREATE TABLE IF NOT EXISTS ticket_settings (
         id INT AUTO_INCREMENT PRIMARY KEY,
         guild_id BIGINT NOT NULL,
         category_id BIGINT NOT NULL,
         support_role_id BIGINT NOT NULL,
         created_at DATETIME NOT NULL,
         deleted_at DATETIME NULL,
         INDEX idx_ticket_settings_guild (guild_id)
     ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;


-- チケット本体
CREATE TABLE IF NOT EXISTS tickets (
         id INT AUTO_INCREMENT PRIMARY KEY,
         guild_id BIGINT NOT NULL,
         channel_id BIGINT NOT NULL,
         user_id BIGINT NOT NULL,
         status ENUM('open','closed') DEFAULT 'open',
         created_at DATETIME NOT NULL,
         closed_at DATETIME NULL,
         INDEX idx_tickets_guild (guild_id),
         INDEX idx_tickets_user (user_id),
         INDEX idx_tickets_channel (channel_id)
     ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

 -- チケットログ（アクション履歴）
CREATE TABLE IF NOT EXISTS ticket_logs (
         id INT AUTO_INCREMENT PRIMARY KEY,
         ticket_id INT NOT NULL,
         action VARCHAR(255) NOT NULL,
         user_id BIGINT NOT NULL,
         timestamp DATETIME NOT NULL,
         FOREIGN KEY (ticket_id) REFERENCES tickets(id) ON DELETE CASCADE,
         INDEX idx_ticket_logs_ticket (ticket_id)
     ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE IF NOT EXISTS pinned_messages (
         message_id BIGINT PRIMARY KEY,
         guild_id BIGINT NOT NULL,
         channel_id BIGINT NOT NULL,
         author_id BIGINT NOT NULL,
         pinned_by_id BIGINT NOT NULL,
         content TEXT NOT NULL,
         created_at DATETIME NOT NULL
     );
 CREATE TABLE IF NOT EXISTS leave_logs (
         id INT AUTO_INCREMENT PRIMARY KEY,
         guild_id BIGINT NOT NULL,
         member_id BIGINT NOT NULL,
         left_at DATETIME NOT NULL,
         member_count INT NOT NULL
     );

CREATE TABLE IF NOT EXISTS leave_settings (
         id INT AUTO_INCREMENT PRIMARY KEY,
         guild_id BIGINT NOT NULL,
         channel_id BIGINT NOT NULL,
         message TEXT NOT NULL,
         created_at DATETIME NOT NULL,
         deleted_at DATETIME DEFAULT NULL
     );

CREATE INDEX idx_leave_settings_guild ON leave_settings(guild_id);
CREATE INDEX idx_leave_logs_guild ON leave_logs(guild_id);
CREATE INDEX idx_leave_logs_member ON leave_logs(member_id);
