import javax.swing.*;
import java.awt.*;
import java.awt.image.*;
import java.io.*;
import javax.imageio.ImageIO;
import java.util.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class ColorPicker {
    private static BufferedImage image;
    private static BufferedImage originalImage;

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            JFrame frame = new JFrame("Image Processing with Java 2D");
            frame.setSize(800, 600);
            frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
            frame.setLayout(new BorderLayout());

            JPanel controlPanel = new JPanel();
            frame.add(controlPanel, BorderLayout.SOUTH);

            JLabel imageLabel = new JLabel();
            frame.add(imageLabel, BorderLayout.CENTER);

            JButton loadButton = new JButton("Load Image");
            controlPanel.add(loadButton);

            JButton histButton = new JButton("Histogram Equalization");
            controlPanel.add(histButton);

            JButton contrastButton = new JButton("Linear Contrast");
            controlPanel.add(contrastButton);

            JButton lowPassButton = new JButton("Low-Pass Filter");
            controlPanel.add(lowPassButton);

            JButton resetButton = new JButton("Reset");
            controlPanel.add(resetButton);

            loadButton.addActionListener(e -> loadImage(imageLabel));
            histButton.addActionListener(e -> applyHistogramEqualization(imageLabel));
            contrastButton.addActionListener(e -> applyLinearContrast(imageLabel));
            lowPassButton.addActionListener(e -> applyLowPassFilter(imageLabel));
            resetButton.addActionListener(e -> resetImage(imageLabel));

            frame.setVisible(true);
        });
    }

    private static void loadImage(JLabel imageLabel) {
        JFileChooser fileChooser = new JFileChooser();
        int result = fileChooser.showOpenDialog(null);
        if (result == JFileChooser.APPROVE_OPTION) {
            try {
                File file = fileChooser.getSelectedFile();
                image = ImageIO.read(file);
                originalImage = new BufferedImage(image.getWidth(), image.getHeight(), BufferedImage.TYPE_INT_RGB);
                Graphics g = originalImage.getGraphics();
                g.drawImage(image, 0, 0, null);
                g.dispose();
                imageLabel.setIcon(new ImageIcon(image));
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    private static void applyHistogramEqualization(JLabel imageLabel) {
        if (image != null) {
            BufferedImage equalizedImage = histogramEqualization(image);
            imageLabel.setIcon(new ImageIcon(equalizedImage));
        }
    }

    private static BufferedImage histogramEqualization(BufferedImage img) {
        int width = img.getWidth();
        int height = img.getHeight();
        BufferedImage resultImage = new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB);

        // Histogram for each color channel (RGB)
        int[] redHist = new int[256];
        int[] greenHist = new int[256];
        int[] blueHist = new int[256];

        for (int y = 0; y < height; y++) {
            for (int x = 0; x < width; x++) {
                Color color = new Color(img.getRGB(x, y));
                redHist[color.getRed()]++;
                greenHist[color.getGreen()]++;
                blueHist[color.getBlue()]++;
            }
        }

        int[] redCdf = new int[256];
        int[] greenCdf = new int[256];
        int[] blueCdf = new int[256];
        redCdf[0] = redHist[0];
        greenCdf[0] = greenHist[0];
        blueCdf[0] = blueHist[0];

        for (int i = 1; i < 256; i++) {
            redCdf[i] = redCdf[i - 1] + redHist[i];
            greenCdf[i] = greenCdf[i - 1] + greenHist[i];
            blueCdf[i] = blueCdf[i - 1] + blueHist[i];
        }

        int totalPixels = width * height;
        for (int i = 0; i < 256; i++) {
            redCdf[i] = (int) ((float) redCdf[i] / totalPixels * 255);
            greenCdf[i] = (int) ((float) greenCdf[i] / totalPixels * 255);
            blueCdf[i] = (int) ((float) blueCdf[i] / totalPixels * 255);
        }

        for (int y = 0; y < height; y++) {
            for (int x = 0; x < width; x++) {
                Color color = new Color(img.getRGB(x, y));
                int r = redCdf[color.getRed()];
                int g = greenCdf[color.getGreen()];
                int b = blueCdf[color.getBlue()];
                resultImage.setRGB(x, y, new Color(r, g, b).getRGB());
            }
        }

        return resultImage;
    }

    private static void applyLinearContrast(JLabel imageLabel) {
        if (image != null) {
            BufferedImage contrastImage = applyLinearContrast(image);
            imageLabel.setIcon(new ImageIcon(contrastImage));
        }
    }

    private static BufferedImage applyLinearContrast(BufferedImage img) {
        int width = img.getWidth();
        int height = img.getHeight();
        BufferedImage resultImage = new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB);

        int min = Integer.MAX_VALUE;
        int max = Integer.MIN_VALUE;

        for (int y = 0; y < height; y++) {
            for (int x = 0; x < width; x++) {
                Color color = new Color(img.getRGB(x, y));
                int gray = (int) (0.2989 * color.getRed() + 0.587 * color.getGreen() + 0.114 * color.getBlue());
                min = Math.min(min, gray);
                max = Math.max(max, gray);
            }
        }

        for (int y = 0; y < height; y++) {
            for (int x = 0; x < width; x++) {
                Color color = new Color(img.getRGB(x, y));
                int gray = (int) (0.2989 * color.getRed() + 0.587 * color.getGreen() + 0.114 * color.getBlue());
                int stretchedGray = (int) ((float) (gray - min) / (max - min) * 255);
                resultImage.setRGB(x, y, new Color(stretchedGray, stretchedGray, stretchedGray).getRGB());
            }
        }

        return resultImage;
    }

    private static void applyLowPassFilter(JLabel imageLabel) {
        if (image != null) {
            BufferedImage filteredImage = applyLowPassFilter(image);
            imageLabel.setIcon(new ImageIcon(filteredImage));
        }
    }

    // Применение низкочастотного фильтра (средний фильтр)
    private static BufferedImage applyLowPassFilter(BufferedImage img) {
        int width = img.getWidth();
        int height = img.getHeight();
        BufferedImage resultImage = new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB);

        // Применение среднего фильтра 3x3
        int filterSize = 3;
        int offset = filterSize / 2;

        for (int y = offset; y < height - offset; y++) {
            for (int x = offset; x < width - offset; x++) {
                int sumR = 0, sumG = 0, sumB = 0;
                for (int ky = -offset; ky <= offset; ky++) {
                    for (int kx = -offset; kx <= offset; kx++) {
                        Color color = new Color(img.getRGB(x + kx, y + ky));
                        sumR += color.getRed();
                        sumG += color.getGreen();
                        sumB += color.getBlue();
                    }
                }

                int avgR = sumR / (filterSize * filterSize);
                int avgG = sumG / (filterSize * filterSize);
                int avgB = sumB / (filterSize * filterSize);

                resultImage.setRGB(x, y, new Color(avgR, avgG, avgB).getRGB());
            }
        }

        return resultImage;
    }

    private static void resetImage(JLabel imageLabel) {
        if (originalImage != null) {
            image = new BufferedImage(originalImage.getWidth(), originalImage.getHeight(), BufferedImage.TYPE_INT_RGB);
            Graphics g = image.getGraphics();
            g.drawImage(originalImage, 0, 0, null);
            g.dispose();
            imageLabel.setIcon(new ImageIcon(image));
        }
    }
}
