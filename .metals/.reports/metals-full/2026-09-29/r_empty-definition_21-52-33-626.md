error id: file:///C:/Users/aagii/OneDrive/Documents/web-development-lab/GamePanel.java:_empty_/keys#remove#
file:///C:/Users/aagii/OneDrive/Documents/web-development-lab/GamePanel.java
empty definition using pc, found symbol in pc: _empty_/keys#remove#
empty definition using semanticdb
empty definition using fallback
non-local guesses:

offset: 705
uri: file:///C:/Users/aagii/OneDrive/Documents/web-development-lab/GamePanel.java
text:
```scala
import java.awt.RenderingHints;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;
import java.awt.geom.Path2D;
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
@@ -33,6 +34,7 @@ public class GamePanel extends JPanel implements Runnable, KeyListener {
    private long menuStartTime = -1;
    private int menuSelection = 0; // 0=start, 1=restart, 2=quit
    private int mapClearSelection = 0; // 0=next, 1=restart, 2=menu
    private int starsEarned = 0;

    private enum State {
        MENU, PLAYING, MAP_CLEAR, GAME_OVER, WIN
@@ -99,6 +101,22 @@ private void handleGlobalShortcuts() {
            state = State.PLAYING;
            keys.rem@@ove(KeyEvent.VK_R);
        }
        if (keys.contains(KeyEvent.VK_T)) {
            if (state == State.PLAYING) {
                if (currentMap >= TOTAL_MAPS - 1) {
                    state = State.WIN;
                } else {
                    currentMap++;
                    loadMap(currentMap);
                    state = State.PLAYING;
                }
            } else if (state == State.MENU) {
                currentMap = 0;
                loadMap(currentMap);
                state = State.PLAYING;
            }
            keys.remove(KeyEvent.VK_T);
        }
    }

    private void loadMap(int index) {
@@ -211,16 +229,30 @@ private void updateGame() {
                }
            }
        }
        long alive = players.stream().filter(p -> p.alive).count();
        if (alive == 0) {
        long alivePlayers = players.stream().filter(p -> p.alive).count();
        if (alivePlayers == 0) {
            state = State.GAME_OVER;
            return;
        }
        if (allAliveAtDoor()) {
            if (keys.contains(KeyEvent.VK_ENTER) || keys.contains(KeyEvent.VK_SPACE)) {
                state = State.MAP_CLEAR;
                stateTimer = System.nanoTime();
                mapClearSelection = 0;
                if (currentMap >= TOTAL_MAPS - 1) {
                    state = State.WIN;
                } else {
                    if (alivePlayers == 4) {
                        starsEarned = 3;
                    } else if (alivePlayers == 3) {
                        starsEarned = 2;
                    } else if (alivePlayers >= 1) {
                        starsEarned = 1;
                    } else {
                        starsEarned = 0;
                    }
                    // Star system: 4 alive = 3 stars, 3 alive = 2 stars, 2 alive = 1 star, 1 alive = 1 star
                    state = State.MAP_CLEAR;
                    stateTimer = System.nanoTime();
                    mapClearSelection = 0;
                }
                keys.remove(KeyEvent.VK_ENTER);
                keys.remove(KeyEvent.VK_SPACE);
            }
@@ -239,7 +271,7 @@ protected void paintComponent(Graphics g) {
            drawGame(g2);
        } else if (state == State.MAP_CLEAR) {
            drawGame(g2);
            drawMapClearMenu(g2);
            drawMapClearScreen(g2);
        } else if (state == State.GAME_OVER) {
            drawGame(g2);
            drawOverlay(g2, "GAME OVER");
@@ -280,7 +312,7 @@ private void drawMenu(Graphics2D g) {
        g.drawString(title, titleX, titleY);

        // Subtitle
        String subtitle = "Найзуудтайгаа хамтран 4 map давна!";
        String subtitle = "Найзуудтайгаа хамтран тоглоорой";
        g.setFont(new Font("Arial", Font.PLAIN, 20));
        FontMetrics sfm = g.getFontMetrics();
        int sx = WIDTH / 2 - sfm.stringWidth(subtitle) / 2;
@@ -338,7 +370,7 @@ private void drawMenu(Graphics2D g) {
        // Footer hint
        g.setFont(new Font("Arial", Font.PLAIN, 13));
        g.setColor(new Color(200, 200, 200, 160));
        g.drawString("Use Up/Down or W/S then ENTER. ESC returns to menu. R restarts.", WIDTH / 2 - 240, HEIGHT - 20);
        g.drawString("Use Up/Down or W/S then ENTER. ESC returns to menu. R restarts.", WIDTH / 2 - 320, HEIGHT - 20);
    }

    private void drawGame(Graphics2D g) {
@@ -405,7 +437,7 @@ private void drawGame(Graphics2D g) {
        if (aliveCount() > 0) {
            long atDoor = aliveAtDoorCount();
            if (allAliveAtDoor()) {
                doorText = "Амьд үлдсэн бүх тоглогчид хаалган дээр ирлээ. [ENTER] дарж дараагийн үе рүү шилжинэ үү!";
                doorText = "Амьд үлдсэн бүх тоглогчид хаалган дээр ирлээ. [ENTER] дарж дараагийн үе рүү шилжинэ!";
            } else if (atDoor > 0) {
                doorText = "Хаалган дээр: " + atDoor + "/" + aliveCount() + " тоглогч байна.";
            }
@@ -432,7 +464,8 @@ private void drawOverlay(Graphics2D g, String text) {
        drawCenteredString(g, text, WIDTH / 2, HEIGHT / 2 - 50);
    }

    private void drawMapClearMenu(Graphics2D g) {
    private void drawMapClearScreen_UNUSED(Graphics2D g) {

        drawOverlay(g, "Үе давлаа!");
        String[] options = { "Дараагийн үе", "Дахин эхлүүлэх", "Menu руу буцах" };
        int menuX = WIDTH / 2;
@@ -455,22 +488,190 @@ private void drawMapClearMenu(Graphics2D g) {
        g.drawString("Use Up/Down or W/S then ENTER.", WIDTH / 2 - 140, menuY + options.length * 72);
    }

    private void drawMapClearOverlay_UNUSED(Graphics2D g) {
        g.setColor(new Color(0, 0, 0, 180));
        g.fillRect(0, 0, WIDTH, HEIGHT);

        g.setColor(Color.GREEN);
        g.setFont(new Font("Arial", Font.BOLD, 64));
        drawCenteredString(g, "LEVEL CLEARED!", WIDTH / 2, HEIGHT / 2 - 120);

        int starSize = 50;
        int spacing = 70;
        int startX = WIDTH / 2 - spacing;
        int startY = HEIGHT / 2 + 10;

        for (int i = 0; i < 3; i++) {
            boolean filled = (i < starsEarned);
            drawStar(g, startX + (i * spacing), startY, starSize / 2, starSize, filled);
        }

        g.setFont(new Font("Arial", Font.PLAIN, 18));
        g.setColor(Color.WHITE);
        drawCenteredString(g, "Амьд үлдсэн тоглогчид: " + aliveCount() + "  |  Авсан од: " + starsEarned,
                WIDTH / 2, startY + starSize + 40);
    }

    private void drawMapClearScreen(Graphics2D g) {
        // Dark semi-transparent background
        g.setColor(new Color(10, 15, 30, 215));
        g.fillRect(0, 0, WIDTH, HEIGHT);

        // Animated shimmer for title
        double animT = (System.nanoTime() % 3_000_000_000L) / 3_000_000_000.0;
        int shimmer = (int)(Math.abs(Math.sin(animT * Math.PI)) * 40);

        // Title
        g.setFont(new Font("Verdana", Font.BOLD, 68));
        FontMetrics tfm = g.getFontMetrics();
        String title = "ҮЕ ДАВЛАА!";
        int titleX = WIDTH / 2 - tfm.stringWidth(title) / 2;
        g.setColor(new Color(0, 0, 0, 150));
        g.drawString(title, titleX + 4, 184);
        g.setColor(new Color(255, 210 + shimmer / 2, 50));
        g.drawString(title, titleX, 180);

        // Map progress label
        g.setFont(new Font("Arial", Font.PLAIN, 22));
        g.setColor(new Color(180, 200, 255, 200));
        String mapLabel = "Map " + (currentMap + 1) + " / " + TOTAL_MAPS;
        FontMetrics mlm = g.getFontMetrics();
        g.drawString(mapLabel, WIDTH / 2 - mlm.stringWidth(mapLabel) / 2, 222);

        // Stars
        int outerR = 48;
        int innerR = 20;
        int starSpacing = 130;
        int starsY = 315;
        int startX = WIDTH / 2 - starSpacing;
        for (int i = 0; i < 3; i++) {
            drawStar(g, startX + i * starSpacing, starsY, innerR, outerR, i < starsEarned);
        }

        // Star subtitle
        g.setFont(new Font("Arial", Font.PLAIN, 18));
        g.setColor(new Color(210, 220, 240, 210));
        String starLabel = "Авсан од: " + starsEarned + " / 3     Амьд үлдсэн: " + aliveCount() + " / 4";
        FontMetrics slm = g.getFontMetrics();
        g.drawString(starLabel, WIDTH / 2 - slm.stringWidth(starLabel) / 2, starsY + outerR + 38);


        // Menu buttons
        String[] options = { "Дараагийн үе", "Дахин эхлүүлэх", "Menu руу буцах" };
        int menuX = WIDTH / 2;
        int menuStartY = starsY + outerR + 75;
        int boxW = 340, boxH = 52, gap = 14;
        for (int i = 0; i < options.length; i++) {
            boolean sel = (i == mapClearSelection);
            int bx = menuX - boxW / 2;
            int by = menuStartY + i * (boxH + gap);
            g.setColor(sel ? new Color(50, 180, 100) : new Color(35, 40, 60, 200));
            g.fillRoundRect(bx, by, boxW, boxH, 16, 16);
            if (!sel) {
                g.setColor(new Color(80, 90, 120, 160));
                g.drawRoundRect(bx, by, boxW, boxH, 16, 16);
            }
            g.setColor(sel ? Color.WHITE : new Color(190, 200, 220));
            g.setFont(new Font("Arial", sel ? Font.BOLD : Font.PLAIN, sel ? 20 : 17));
            FontMetrics bfm = g.getFontMetrics();
            g.drawString(options[i], menuX - bfm.stringWidth(options[i]) / 2,
                    by + boxH / 2 + bfm.getAscent() / 2 - 2);
        }

        // Footer
        g.setFont(new Font("Arial", Font.PLAIN, 13));
        g.setColor(new Color(150, 155, 175, 150));
        String hint = "↑↓ эсвэл W/S → сонгох   |   ENTER → баталгаажуулах";
        FontMetrics hfm = g.getFontMetrics();
        g.drawString(hint, WIDTH / 2 - hfm.stringWidth(hint) / 2, HEIGHT - 22);
    }

    private void drawStar(Graphics2D g, double x, double y, double innerRadius, double outerRadius, boolean filled) {
        Path2D.Double path = new Path2D.Double();
        double numPoints = 5;
        double angleIncrement = Math.PI / numPoints;
        double currentAngle = -Math.PI / 2;

        path.moveTo(x + outerRadius * Math.cos(currentAngle), y + outerRadius * Math.sin(currentAngle));
        currentAngle += angleIncrement;

        for (int i = 1; i < numPoints * 2; i++) {
            double r = (i % 2 == 0) ? outerRadius : innerRadius;
            path.lineTo(x + r * Math.cos(currentAngle), y + r * Math.sin(currentAngle));
            currentAngle += angleIncrement;
        }
        path.closePath();

        if (filled) {
            g.setColor(Color.YELLOW);
            g.fill(path);
        }

        g.setColor(new Color(255, 200, 0));
        g.setStroke(new java.awt.BasicStroke(2));
        g.draw(path);
    }

    private void drawWin(Graphics2D g) {
        g.setColor(new Color(8, 8, 25));
        // Deep space background
        g.setColor(new Color(8, 10, 28));
        g.fillRect(0, 0, WIDTH, HEIGHT);
        Color[] c = { Color.RED, Color.BLUE, Color.GREEN, Color.YELLOW, Color.PINK };
        for (int i = 0; i < 30; i++) {
            g.setColor(c[i % c.length]);
            g.fillRect((i * 43 + 10) % WIDTH, (i * 71 + 20) % HEIGHT, 15, 15);

        // "YOU WIN!" title with glow
        g.setFont(new Font("Verdana", Font.BOLD, 88));
        FontMetrics tfm = g.getFontMetrics();
        String title = "YOU WIN!";
        int titleX = WIDTH / 2 - tfm.stringWidth(title) / 2;
        // glow layers
        for (int glow = 3; glow >= 1; glow--) {
            g.setColor(new Color(255, 150, 0, 40 * glow));
            g.drawString(title, titleX - glow, 185 - glow);
            g.drawString(title, titleX + glow, 185 + glow);
        }
        g.setColor(Color.ORANGE);
        g.setFont(new Font("Arial", Font.BOLD, 72));
        drawCenteredString(g, "YOU WIN!", WIDTH / 2, 250);
        g.setFont(new Font("Arial", Font.PLAIN, 32));
        drawCenteredString(g, "Та чадлаа!", WIDTH / 2, 340);
        g.setFont(new Font("Arial", Font.BOLD, 24));
        g.setColor(Color.YELLOW);
        drawCenteredString(g, "Тоглосонд баярлалаа!", WIDTH / 2, 420);
        // shadow
        g.setColor(new Color(0, 0, 0, 180));
        g.drawString(title, titleX + 5, 190);
        // main
        g.setColor(new Color(255, 200, 50));
        g.drawString(title, titleX, 185);

        // Subtitle
        g.setFont(new Font("Arial", Font.PLAIN, 28));
        g.setColor(new Color(200, 230, 255, 220));
        String sub = "Та бүх үеийг амжилттай давлаа!";
        FontMetrics sfm = g.getFontMetrics();
        g.drawString(sub, WIDTH / 2 - sfm.stringWidth(sub) / 2, 240);

        // 3 big gold stars
        int outerR = 56;
        int innerR = 24;
        int starSpacing = 140;
        int starsY = 340;
        int starStartX = WIDTH / 2 - starSpacing;
        for (int i = 0; i < 3; i++) {
            drawStar(g, starStartX + i * starSpacing, starsY, innerR, outerR, true);
        }

        // Final stats
        g.setFont(new Font("Arial", Font.PLAIN, 20));
        g.setColor(new Color(220, 230, 255, 200));
        String stats = "Амьд үлдсэн: " + aliveCount() + " / 4";
        FontMetrics stfm = g.getFontMetrics();
        g.drawString(stats, WIDTH / 2 - stfm.stringWidth(stats) / 2, starsY + outerR + 44);

        // Bottom message
        g.setFont(new Font("Arial", Font.BOLD, 22));
        g.setColor(new Color(255, 220, 80));
        String thanks = "Тоглосонд баярлалаа!";
        FontMetrics bfm = g.getFontMetrics();
        g.drawString(thanks, WIDTH / 2 - bfm.stringWidth(thanks) / 2, starsY + outerR + 90);

        // ESC hint
        g.setFont(new Font("Arial", Font.PLAIN, 14));
        g.setColor(new Color(150, 155, 175, 150));
        String hint = "ESC дарж Menu руу буцах";
        FontMetrics hfm = g.getFontMetrics();
        g.drawString(hint, WIDTH / 2 - hfm.stringWidth(hint) / 2, HEIGHT - 22);
    }

    private void drawCenteredString(Graphics2D g, String text, int centerX, int centerY) {
```


#### Short summary: 

empty definition using pc, found symbol in pc: _empty_/keys#remove#