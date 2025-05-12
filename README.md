# Survey Form Builder (ReactJS)
## Date: 12-05-2025

## AIM
To create a Survey Form Builder using ReactJS..

## ALGORITHM
### STEP 1 Initialize States
mode ← 'build' (default mode)

questions ← [] (holds all survey questions)

currentQuestion ← { text: '', type: 'text', options: '' } (holds input while building)

editingIndex ← null (tracks which question is being edited)

responses ← {} (user responses to survey)

submitted ← false (indicates whether survey was submitted)

### STEP 2 Switch Modes
Toggle mode between 'build' and 'fill' when the toggle button is clicked.

Reset submitted to false when entering Fill Mode.

### STEP 3 Build Mode Logic
#### a. Add/Update Question
If currentQuestion.text is not empty:

If type is "radio" or "checkbox", split currentQuestion.options by commas → optionsArray.

Construct a questionObject:

If editingIndex is not null, replace question at that index.

Else, push questionObject into questions.

Reset currentQuestion and editingIndex.

#### b. Edit Question
Set currentQuestion to the selected question’s data.

Set editingIndex to the question’s index.

#### c. Delete Question
Remove the selected question from questions.

### STEP 4 Fill Mode Logic
#### a. Render Form
For each question in questions:

If type is "text", render a text input.

If type is "radio", render radio buttons for each option.

If type is "checkbox", render checkboxes.

#### b. Capture Responses
On input change:

For "text" and "radio", update responses[index] = value.

For "checkbox":

If option exists in responses[index], remove it.

Else, add it to the array.

### STEP 5 Submit Form
When user clicks "Submit":

Set submitted = true.

Display a summary using the questions and responses.

### STEP 6 Display Summary
Iterate over questions.

Display the response from responses[i] for each question:

If it's an array, join it with commas.

If empty, show "No response".


## PROGRAM
app.css
```
.post-event-survey-container {
  width: 100vw;
  height: 100vh;
  margin: 0; 
  padding: 0;
  display: flex; 
  flex-direction: column;
  align-items: center; 
  justify-content: flex-start; 
  background-color: #82c1dc;
  border-radius: 0;
  box-shadow: none; /
  font-family: Arial, sans-serif;
  color: #222;
  overflow-x: hidden;
}

.survey-header {
  text-align: left;
  margin-bottom: 20px;
  padding-bottom: 10px;
  border-bottom: 1px solid #a34c4c;
  width: 80%;
  max-width: 800px;
}

.survey-header h1 {
  font-size: 24px;
  color: #0a4c83;
  margin-bottom: 10px;
  font-weight: bold;
}

.survey-header p {
  font-size: 14px;
  color: #02040a;
}

.survey-form {
  margin-top: 20px;
  width: 80%; 
  max-width: 800px; 
}

.form-group {
  margin-bottom: 25px;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  font-weight: bold;
  color: #3a6269;
  font-size: 16px;
}

.input-field {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  box-sizing: border-box;
  font-size: 14px;
  background-color: #f8f8f8;
  color: #280808;
}

.input-field:focus {
  outline: none;
  border-color: #4CAF50;
  box-shadow: 0 0 3px rgba(76, 175, 80, 0.3);
}

.textarea-field {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  box-sizing: border-box;
  font-size: 14px;
  resize: vertical;
  background-color: #f3f3f6;
  color: #333;
}

.textarea-field:focus {
  outline: none;
  border-color: #4CAF50;
  box-shadow: 0 0 3px rgba(76, 175, 80, 0.3);
}

.radio-group {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.radio-group label {
  display: flex;
  align-items: center;
  gap: 10px;
  font-weight: normal;
  font-size: 14px;
  color: #444;
}

.radio-group input[type="radio"] {
  margin: 0;
}

.checkbox-group {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-top: 10px;
}

.checkbox-group label {
  display: flex;
  align-items: center;
  gap: 10px;
  font-weight: normal;
  font-size: 14px;
  color: #444;
}

.checkbox-group input[type="checkbox"] {
  margin: 0;
}

.button-group {
  display: flex;
  justify-content: flex-end;
  margin-top: 30px;
  gap: 15px;
  width: 80%; 
  max-width: 800px;
}

.cancel-button,
.next-button {
  padding: 12px 25px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
  font-weight: bold;
  box-shadow: 0 2px 2px rgba(0, 0, 0, 0.1);
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
}

.cancel-button {
  background-color: #e53935;
  color: white;
}

.cancel-button:hover {
  background-color: #c62828;
  box-shadow: 0 3px 3px rgba(0, 0, 0, 0.2);
}

.next-button {
  background-color: #43a047;
  color: white;
}

.next-button:hover {
  background-color: #6bb4a7;
  box-shadow: 0 3px 3px rgba(78, 131, 147, 0.2);
}

@media (max-width: 600px) {
  .post-event-survey-container {
    padding: 0; 
  }
  .survey-header{
    width: 95%;
  }
  .survey-form {
    width: 95%;
  }
  .button-group{
    width: 95%;
  }

  .radio-group {
    flex-direction: column;
  }

  .button-group {
    flex-direction: column;
    gap: 10px;
  }
}
```
app.jsx
```
import React, { useState } from 'react';
import './App.css';

const PostEventSurvey = () => {
  const [formData, setFormData] = useState({
    name: '',
    dept: '',
    hearAboutEvent: '',
    eventRating: '',
    contentRating: '',
    volunteerInterest: '',
    comments: '',
    technicalEvents: [], 
  });

  const [allTechnicalEvents] = useState([ 
    'Web Development Workshop',
    'Data Science Seminar',
    'Mobile App Development',
    'Cloud Computing Basics',
    'Cybersecurity Fundamentals',
    'Game Development',
    'AI and Machine Learning',
    'UI/UX Design'
  ]);

    const handleTechEventChange = (e) => {
    const { value, checked } = e.target;
    const currentTechEvents = formData.technicalEvents;

    if (checked) {
     
      setFormData({
        ...formData,
        technicalEvents: [...currentTechEvents, value],
      });
    } else {
     
        setFormData({
            ...formData,
            technicalEvents: currentTechEvents.filter((event) => event !== value),
        });
    }
  };


  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData({ ...formData, [name]: value });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log(formData); 
    alert('Thank you for your feedback!');
  
    setFormData({
      name: '',
      dept: '',
      hearAboutEvent: '',
      eventRating: '',
      contentRating: '',
      volunteerInterest: '',
      comments: '',
      technicalEvents: [], 
    });
  };

  return (
    <div className="post-event-survey-container">
      <div className="survey-header">
        <h1>Post Event Survey</h1>
        <p>Required fields are marked with an asterisk (*)</p>
      </div>
      <form onSubmit={handleSubmit} className="survey-form">
        <div className="form-group">
          <label htmlFor="name">1. Name: *</label>
          <input
            type="text"
            id="name"
            name="name"
            value={formData.name}
            onChange={handleChange}
            required
            className="input-field"
          />
        </div>

        <div className="form-group">
          <label htmlFor="dept">2. Department: *</label>
          <input
            type="text"
            id="dept"
            name="dept"
            value={formData.dept}
            onChange={handleChange}
            required
            className="input-field"
          />
        </div>

        <div className="form-group">
          <label htmlFor="hearAboutEvent">
            3. How did you hear about this event? *
          </label>
          <input
            type="text"
            id="hearAboutEvent"
            name="hearAboutEvent"
            value={formData.hearAboutEvent}
            onChange={handleChange}
            required
            className="input-field"
          />
        </div>

        <div className="form-group">
          <label>4. How do you rate the event location? *</label>
          <div className="radio-group">
            <label>
              <input
                type="radio"
                name="eventRating"
                value="1-Poor"
                checked={formData.eventRating === '1-Poor'}
                onChange={handleChange}
                required
              />
              1 - Poor
            </label>
            <label>
              <input
                type="radio"
                name="eventRating"
                value="2-BelowAverage"
                checked={formData.eventRating === '2-BelowAverage'}
                onChange={handleChange}
              />
              2 - Below Average
            </label>
            <label>
              <input
                type="radio"
                name="eventRating"
                value="3-Average"
                checked={formData.eventRating === '3-Average'}
                onChange={handleChange}
              />
              3 - Average
            </label>
            <label>
              <input
                type="radio"
                name="eventRating"
                value="4-Good"
                checked={formData.eventRating === '4-Good'}
                onChange={handleChange}
              />
              4 - Good
            </label>
            <label>
              <input
                type="radio"
                name="eventRating"
                value="5-Excellent"
                checked={formData.eventRating === '5-Excellent'}
                onChange={handleChange}
              />
              5 - Excellent
            </label>
          </div>
        </div>

        <div className="form-group">
          <label>5. How do you rate the content that was presented? *</label>
          <div className="radio-group">
            <label>
              <input
                type="radio"
                name="contentRating"
                value="Poor"
                checked={formData.contentRating === 'Poor'}
                onChange={handleChange}
                required
              />
              Poor
            </label>
            <label>
              <input
                type="radio"
                name="contentRating"
                value="BelowAverage"
                checked={formData.contentRating === 'BelowAverage'}
                onChange={handleChange}
              />
              Below Average
            </label>
            <label>
              <input
                type="radio"
                name="contentRating"
                value="Average"
                checked={formData.contentRating === 'Average'}
                onChange={handleChange}
              />
              Average
            </label>
            <label>
              <input
                type="radio"
                name="contentRating"
                value="Good"
                checked={formData.contentRating === 'Good'}
                onChange={handleChange}
              />
              Good
            </label>
            <label>
              <input
                type="radio"
                name="contentRating"
                value="Excellent"
                checked={formData.contentRating === 'Excellent'}
                onChange={handleChange}
              />
              Excellent
            </label>
          </div>
        </div>

        <div className="form-group">
          <label>6. Would you be interested in volunteering at our next event? *</label>
          <div className="radio-group">
            <label>
              <input
                type="radio"
                name="volunteerInterest"
                value="Yes"
                checked={formData.volunteerInterest === 'Yes'}
                onChange={handleChange}
                required
              />
              Yes
            </label>
            <label>
              <input
                type="radio"
                name="volunteerInterest"
                value="No"
                checked={formData.volunteerInterest === 'No'}
                onChange={handleChange}
              />
              No
            </label>
          </div>
        </div>

        <div className="form-group">
          <label>7. Which technical events did you attend? *</label>
           <div className="checkbox-group">
            {allTechnicalEvents.map((event) => (
              <label key={event}>
                <input
                  type="checkbox"
                  name="technicalEvents"
                  value={event}
                  checked={formData.technicalEvents.includes(event)}
                  onChange={handleTechEventChange}
                />
                {event}
              </label>
            ))}
          </div>
        </div>

        <div className="form-group">
          <label htmlFor="comments">
            8. Please list any other comments or suggestions about the event.
          </label>
          <textarea
            id="comments"
            name="comments"
            value={formData.comments}
            onChange={handleChange}
            rows="4"
            className="textarea-field"
          />
        </div>

        <div className="button-group">
          <button type="button" className="cancel-button">
            Cancel
          </button>
          <button type="submit" className="next-button">
            Next
          </button>
        </div>
      </form>
    </div>
  );
};

export default PostEventSurvey;
```

## OUTPUT
![image](https://github.com/user-attachments/assets/bae7f9c1-1993-4de8-bd1b-8f93410fed50)
![image](https://github.com/user-attachments/assets/5f7bb680-b6f6-499d-997f-720d7807ff7a)
![image](https://github.com/user-attachments/assets/c358365a-a313-4266-ad3d-adfd9a90ee08)









## RESULT
The program for creating Survey Form Builder using ReactJS is executed successfully.
