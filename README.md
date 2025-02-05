kubectl -n ucp get secret keystone-nc -o jsonpath='{.data.keystone\.nc\.json}' | base64 --decode
