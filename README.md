sudo KUBECONFIG=/etc/kubernetes/admin.conf kubectl -n ucp get secret keystone-etc -o jsonpath='{.data.keystone\.nc\.json}' | base64 -d | jq -r '.. | objects | select(has("user")) | .user'
