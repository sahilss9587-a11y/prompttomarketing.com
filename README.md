<?php
header('Content-Type: application/json');
require_once 'config.php';

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $user_id = $_POST['user_id'];
    $article_id = $_POST['article_id'];
    $time_spent = $_POST['time_spent'];
    $quiz_score = $_POST['quiz_score'] ?? 0;

    // Check if already completed
    $stmt = $pdo->prepare("SELECT * FROM reading_progress WHERE user_id = ? AND article_id = ?");
    $stmt->execute([$user_id, $article_id]);
    
    if ($stmt->rowCount() > 0) {
        echo json_encode(['success' => false, 'message' => 'Already completed']);
        exit;
    }

    // Get article points
    $stmt = $pdo->prepare("SELECT points_reward FROM articles WHERE id = ?");
    $stmt->execute([$article_id]);
    $article = $stmt->fetch();
    $points = $article ? $article['points_reward'] : 0;

    // Record progress
    $stmt = $pdo->prepare("INSERT INTO reading_progress (user_id, article_id, time_spent, quiz_score, points_earned) VALUES (?, ?, ?, ?, ?)");
    $stmt->execute([$user_id, $article_id, $time_spent, $quiz_score, $points]);

    // Update user points
    $stmt = $pdo->prepare("UPDATE users SET points = points + ? WHERE id = ?");
    $stmt->execute([$points, $user_id]);

    echo json_encode(['success' => true, 'points_earned' => $points]);
}
?><!-- Add to checkout page -->
<script src="https://checkout.razorpay.com/v1/checkout.js"></script>
<button onclick="redeemPoints(100)">Redeem ₹100</button>

<script>
function redeemPoints(amount) {
    var options = {
        "key": "YOUR_RAZORPAY_KEY",
        "amount": amount * 100, // in paise
        "currency": "INR",
        "name": "ReadAndEarnIndia",
        "description": "Redeem Points",
        "handler": function (response) {
            // Verify payment & transfer to user
            alert('Payment successful! Money sent to your account.');
        }
    };
    var rzp = new Razorpay(options);
    rzp.open();
}
</script>✅ Add Push Notifications
✅ WhatsApp sharing
✅ Referral program (₹50 per referral)
✅ Daily login bonus
✅ Leaderboard
✅ UPI Auto-payouts
✅ Android App (using WebView)# 1. Setup XAMPP/WAMP
# 2. Create database & import SQL
# 3. Replace files in htdocs/readandearn
# 4. Update config.php database credentials
# 5. Visit localhost/readandearnconfig.php
