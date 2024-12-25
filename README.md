<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Highlight and Comment</title>

  <style>
    .highlight {
      background-color: yellow;
      position: relative;
    }

    .comment-icon {
      position: absolute;
      top: -11px;
      right: -12px;
      font-size: 13px;
      cursor: pointer;
    }

    .comment-icon:hover {
      color: darkblue;
    }

    .comment-box {
      position: fixed;
      top: 20%;
      left: 50%;
      transform: translateX(-50%);
      width: 350px;
      background: #fff;
      border: 1px solid #ccc;
      padding: 15px;
      box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
      z-index: 1000;
    }

    .comments-list {
      max-height: 100px;
      overflow-y: auto;
      margin: 10px 0;
      padding: 0;
      list-style: none;
    }

    .comments-list li {
      padding: 5px 0;
      border-bottom: 1px solid #ddd;
    }

    textarea {
      width: 100%;
      margin-top: 10px;
      padding: 5px;
    }

    button {
      padding: 8px 15px;
      background-color: #4CAF50; /* Green */
      color: white;
      border: none;
      cursor: pointer;
      margin-right: 10px;
      border-radius: 5px;
    }

    button:hover {
      background-color: #45a049;
    }

    .close-btn {
      background-color: #f44336; /* Red */
    }

    .close-btn:hover {
      background-color: #e53935;
    }

    .remove-btn {
      background-color: #ff9800; /* Orange */
    }

    .remove-btn:hover {
      background-color: #f57c00;
    }

    .button-container {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-top: 15px;
    }
  </style>
</head>
<body>
  <div id="content">
    <p id="paragraph">Lorem ipsum dolor sit amet consectetur, adipisicing elit. Itaque maxime unde veritatis soluta atque quisquam quia corporis vitae eius quod.</p>
  </div>

  <script>
    let highlightedSpans = []; // Array to keep track of highlighted spans

    // Listen for mouseup events to capture the selected text
    document.addEventListener("mouseup", () => {
      const selectedText = window.getSelection().toString();

      if (selectedText) {
        const selectionRange = window.getSelection().getRangeAt(0);
        const span = document.createElement("span");
        span.className = "highlight";
        span.dataset.comments = JSON.stringify([]); // Initialize empty comments array
        span.dataset.id = Date.now(); // Assign a unique id to each highlighted span

        selectionRange.surroundContents(span);
        window.getSelection().removeAllRanges();

        const icon = document.createElement("span");
        icon.className = "comment-icon";
        icon.textContent = "💬";
        icon.title = "View Comments";
        span.appendChild(icon);

        highlightedSpans.push(span);

        // Attach click event to the comment icon
        icon.addEventListener("click", () => {
          showComments(span);
        });

        // Automatically open the comment box when text is selected
        showComments(span);
      }
    });

    // Function to show the comment box for a highlighted span
    function showComments(span) {
      const existingCommentBox = document.querySelector(`[data-span-id="${span.dataset.id}"]`);

      if (existingCommentBox) {
        existingCommentBox.style.display = "block"; // Show the existing comment box

        // Load and display existing comments from the span's dataset
        const commentsList = existingCommentBox.querySelector(".comments-list");
        const existingComments = JSON.parse(span.dataset.comments);
        commentsList.innerHTML = existingComments
          .map(comment => `<li>${comment}</li>`)
          .join("");
      } else {
        const commentBox = document.createElement("div");
        commentBox.classList.add("comment-box");
        commentBox.setAttribute("data-span-id", span.dataset.id);

        const commentsList = document.createElement("ul");
        commentsList.classList.add("comments-list");

        const newCommentTextarea = document.createElement("textarea");
        newCommentTextarea.placeholder = "Add a new comment...";

        const addCommentButton = document.createElement("button");
        addCommentButton.textContent = "Add Comment";
        addCommentButton.classList.add("add-comment-btn");

        const closeButton = document.createElement("button");
        closeButton.textContent = "Close";
        closeButton.classList.add("close-btn");

        const removeButton = document.createElement("button");
        removeButton.textContent = "Remove Highlight";
        removeButton.classList.add("remove-btn");

        const buttonContainer = document.createElement("div");
        buttonContainer.classList.add("button-container");

        buttonContainer.appendChild(removeButton);
        buttonContainer.appendChild(addCommentButton);
        buttonContainer.appendChild(closeButton);

        commentBox.appendChild(commentsList);
        commentBox.appendChild(newCommentTextarea);
        commentBox.appendChild(buttonContainer);

        document.body.appendChild(commentBox);

        // Load and display existing comments from the span's dataset
        const existingComments = JSON.parse(span.dataset.comments);
        commentsList.innerHTML = existingComments
          .map(comment => `<li>${comment}</li>`)
          .join("");

        removeButton.onclick = () => {
          span.classList.remove("highlight");
          const icon = span.querySelector(".comment-icon");
          if (icon) icon.remove();
          commentBox.remove();
        };

        closeButton.onclick = () => {
          commentBox.style.display = "none"; // Hide the comment box temporarily
        };

        addCommentButton.onclick = () => {
          const newComment = newCommentTextarea.value;
          if (newComment.trim()) {
            const comments = JSON.parse(span.dataset.comments);
            comments.push(newComment);
            span.dataset.comments = JSON.stringify(comments);

            commentsList.innerHTML = comments
              .map(comment => `<li>${comment}</li>`)
              .join("");

            newCommentTextarea.value = "";
          }
        };
      }
    }
  </script>
</body>
</html>
