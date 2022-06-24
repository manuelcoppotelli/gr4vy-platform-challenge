load('ext://uibutton', 'cmd_button', 'location', 'text_input')

docker_compose('docker-compose.yaml')

dc_resource('redis', labels=['backing'])

dc_resource('gateway', labels=['infra'])

dc_resource('auth', resource_deps=['gateway'], labels=['services'])
docker_build('gr4vy.dev/auth', 'auth-api',
  live_update = [
    sync('auth-api', '/app'),
    run('pip install --no-cache-dir -r requirements.txt', trigger='auth-api/requirements.txt'),
    restart_container()
  ]
)
cmd_button(name='token',
    text='Token',
    resource='auth',
    location=location.RESOURCE,
    icon_name='key',
    inputs=[
      text_input('AUTH_USER', label='username', default='alice'),
      text_input('AUTH_PASS', label='password', default='password'),
    ],
    argv=['/bin/sh', '-c', 'http --ignore-stdin -v POST http://localhost/auth/token username=$AUTH_USER password=$AUTH_PASS'],
)

dc_resource('core', resource_deps=['redis', 'gateway'], labels=['services'])
docker_build('gr4vy.dev/core', 'core-api',
  live_update = [
    sync('core-api', '/app'),
    run('pip install --no-cache-dir -r requirements.txt', trigger='core-api/requirements.txt'),
    restart_container()
  ]
)
cmd_button(name='transaction',
    text='Transaction',
    resource='core',
    location=location.RESOURCE,
    icon_name='paid',
    inputs=[
      text_input('CORE_AMOUNT', label='amount', default='100'),
      text_input('CORE_CURRENCY', label='currency', default='USD'),
      text_input('CORE_TOKEN', label='token'),
    ],
    argv=['/bin/sh', '-c', 'http --ignore-stdin -v POST http://localhost/transaction amount=$CORE_AMOUNT currency=$CORE_CURRENCY token=$CORE_TOKEN'],
)

dc_resource('psp', resource_deps=['redis'], labels=['services'])
docker_build('gr4vy.dev/psp', 'psp-connector',
  live_update = [
    sync('psp-connector', '/app'),
    run('pip install --no-cache-dir -r requirements.txt', trigger='psp-connector/requirements.txt'),
    restart_container()
  ]
)
