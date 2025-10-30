flowchart TD
    start[Start] --> visit[User visits Exotic Roleplay Gateway]
    visit --> checkAuth[Check if user is authenticated]
    checkAuth -->|No| authChoice[Choose sign up or sign in]
    authChoice --> signUp[Sign up page]
    authChoice --> signIn[Sign in page]
    signUp --> submitSignUp[Submit sign up form]
    signIn --> submitSignIn[Submit sign in form]
    submitSignUp --> processSignUp[Process sign up via API]
    submitSignIn --> processSignIn[Process sign in via API]
    processSignUp -->|Success| setSession[Set authentication session]
    processSignIn -->|Success| setSession
    processSignUp -->|Failure| showAuthError[Show authentication error]
    processSignIn -->|Failure| showAuthError
    setSession --> dashboard[User is redirected to Dashboard]
    dashboard --> verifyFlow[Start Verification Flow]
    verifyFlow --> showVerifyForm[Show verification form]
    showVerifyForm --> submitVerify[Submit captcha and data]
    submitVerify --> verifyCaptcha[Verify captcha server side]
    verifyCaptcha -->|Fail| showVerifyError[Show verification error]
    verifyCaptcha -->|Pass| encryptData[Encrypt IP address]
    encryptData --> saveDB[Save verification to database]
    saveDB --> sendWebhook[Send Discord webhook notification]
    sendWebhook --> showVerifySuccess[Show verification success]
    showAuthError --> authChoice
    showVerifyError --> showVerifyForm