An authentification (Zend_Auth) Adapter for use with a MongoDB (NoSQL) Database.

Here is an example howto use the adapter (it's very similar to how you use the mysql adapter), some methods have slightly different names:

	# AuthController.php
	
    public function loginAction() {

        $form = new User_Form_LoginForm();

        if ($this->_request->isPost()) {

            $formData = $this->_request->getPost();

            if ($form->isValid($formData)) {
	
				$formData = $form->getValues();

				$userCollection = $mongoConnection->selectCollection('user');
				
				$userConfiguration = Zend_Registry::get('UserConfiguration');
							
				// create authAdapter instance
				$authAdapter = new Chris_Auth_Adapter_MongoDB($userCollection);
				$authAdapter	->setIdentityKey('username');
				$authAdapter	->setCredentialKey('password');
				$authAdapter	->setIdentity($formData['username']);
				$authAdapter	->setCredential($formData['password']);
				$authAdapter	->setSalt($userConfiguration->authentification->password->salt);

				$result = $auth->authenticate($authAdapter);

				if ($result->isValid()) {

					$data = $authAdapter->getResultObject(null, 'password');

					$auth->getStorage()->write($data);
					
				} else {
				
					// login failed
				
				}
					
			} else {
			
				// form data aint valid
			
			}
			
		} else {
		
			$this->view->form = $form;
		
		}
		
	}

TODO:

Redo the credential treatement part. Right know it's hardcoded and does only check if a salt was provided. Maybe could allow use to choose between md5 with salt and crypt, with a preference for crypt.

Feedback is welcome ;) Use the Github Issues system or visite me @ http://www.chris.lu