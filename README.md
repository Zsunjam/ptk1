
<!DOCTYPE html>
<html>
<head>
  <title>Kreator zestawów pytań</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 20px;
      background-color: #444444;
      color: #FFCCFF;
    }

    h1 {
      text-align: center;
      color: #CCFFCC;
    }

    #form-container {
      text-align: center;
      margin-bottom: 20px;
    }

    .set-container {
      margin-bottom: 40px;
    }

    .set-title {
      font-weight: bold;
      margin-bottom: 10px;
      color: #CCFFCC;
    }

    .question {
      margin-bottom: 10px;
    }

    .error-container {
      color: #CCFFCC;
      margin-bottom: 20px;
    }
  </style>
</head>
<body>
  <h1>Kreator zestawów pytań</h1>

  <div id="form-container">
    <form action="" method="POST" enctype="multipart/form-data">
      <label for="set-count">Liczba zestawów:</label>
      <input type="number" name="set-count" id="set-count" min="1" value="1">
      <br>
      <label for="questions-per-set">Liczba pytań w zestawie:</label>
      <input type="number" name="questions-per-set" id="questions-per-set" min="1" value="5">
      <br>
      <input type="checkbox" name="unique-sets" id="unique-sets" checked>
      <label for="unique-sets">Unikalne zestawy</label>
      <br>
      <input type="file" name="file-input" id="file-input">
      <br>
      <input type="submit" value="Generuj zestawy">
    </form>
  </div>

  <div id="questions-container">
    <?php
    if ($_SERVER['REQUEST_METHOD'] === 'POST') {
      $setCount = isset($_POST['set-count']) ? intval($_POST['set-count']) : 1;
      $questionsPerSet = isset($_POST['questions-per-set']) ? intval($_POST['questions-per-set']) : 5;
      $uniqueSets = isset($_POST['unique-sets']);

      if (isset($_FILES['file-input']) && $_FILES['file-input']['error'] === UPLOAD_ERR_OK) {
        $fileContent = file_get_contents($_FILES['file-input']['tmp_name']);
        $questions = array_filter(explode("\n", $fileContent), 'trim');

        $totalQuestions = count($questions);
        $maxUniqueSets = floor($totalQuestions / $questionsPerSet);

        if ($uniqueSets && $setCount > $maxUniqueSets) {
          echo '<div class="error-container">';
          echo '<p>Nie można wygenerować ' . $setCount . ' unikalnych zestawów pytań dla podanych parametrów.</p>';
          echo '<p>Maksymalna liczba unikalnych zestawów pytań dla podanych parametrów: ' . $maxUniqueSets . '.</p>';
          echo '</div>';
        } else {
          for ($i = 1; $i <= $setCount; $i++) {
            echo '<div class="set-container">';
            echo '<h2 class="set-title">Zestaw ' . $i . '</h2>';

            for ($j = 1; $j <= $questionsPerSet; $j++) {
              $questionIndex = $uniqueSets ? (($i - 1) * $questionsPerSet + ($j - 1)) : rand(0, $totalQuestions - 1);
              echo '<div class="question">Pytanie ' . $j . ': ' . $questions[$questionIndex] . '</div>';
            }

            echo '</div>';
          }
        }
      }
    }
    ?>
  </div>

</body>
</html>
