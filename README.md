### oauthlib
---
https://github.com/oauthlib/oauthlib

```py
from __future__ import absolute_import, unicode_literals

import json

class TokenEndpointTest(TestCase):
  
  def setUp(self):
    def set_user(request):
      request.user = mock.MagicMock()
      request.client = mock.MagicMock()
      request.client.client_id = 'mocked_client_id'
      return True
      
    self.mock_validator = mock.MagicMock()
    self.mock_validator.authenticate_client.side_effect = set_user
    self.mock_validator.get_code_challege.return_value = None
    self.addCleanup(setattr, self, 'mock_validator', mock.MagicMock())
    auth_code = AuthorizationCodeGrant(
      request_validator=self.mock_validator)
    supported_types = {
      'authorization_code': auth_code,
    }
    self.expires_in = 1800
    token = BearerToken(
      self.mock_validator,
      expires_in=self.expires_in
    )
    self.endpoint = TokenEndpoint(
      'authorization_code',
      default_token_type=token,
      grant_types=supported_types
    )
   
  @mock.patch('oauthlib.common.generate_token', new=lambda: 'abc')
  def test_authorization_grant(self):
    body = 'grant_type=authorization_code&code&code=abc&scope=all+of+them'
    headers, body, status_code = self.endpoint.create_token_response(
      '', body=body)
    token = {
      'token_type': 'Bearer',
      'expires_in': self.expires_in,
      'access_token': 'abc',
      'refresh_token': 'abc',
      'scope': 'all of them',
    }
    self.assertEqual(json.loads(body), token)
    
    body = 'grant_type=authorization_code&code=abc'
    headers, body, status_code = self.endpoint.create_token_response(
      '', body=body)
    token = {
      'token_type': 'Bearer',
      'expires_in': self.expires_in,
      'access_token': 'abc',
      'refresh_token': 'abc'
    }
    self.assertEqual(json.loads(body), token)
    
    body = 'grant_type=authorization_code&code=abc&state=foobar'
    headers, body, status_code = self.endpoint.create_token_response(
      '', body=body)
    self.assertEqual(json.loads(body), token)
    
  def test_missing_type(self):
    _, body, _ = self.endpoint.create_token_response('', body='')
    token = {'error': 'unsupported_grant_type'}
    self.assertEqual(json.loads(body), token)
    
  def test_invvalid_type(self):
    body = 'grant_type=invalid'
    _, body, _ = self.endpoint.create_token_response('', body=body)
    token = {'error': 'unsupported_grant_type'}
    self.assertEqual(json.loads(body), token)
```

```
```

```
```


