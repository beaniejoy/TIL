# ansible builtin modules 정리

## copy

copy 모듈에는 유의사항이 있다.  

```yaml
- name: Copy nginx.conf file to remote server
  ansible.builtin.copy:
    src: nginx.conf
    dest: "{{ nginx_conf_dir }}/"
    owner: "{{ remote_server_user }}"
    group: "{{ remote_server_group }}"
    mode: 0644
```
remote에 `/etc/nginx` 경로에 nginx.conf 파일을 copy하려고 할 때  
dest를 `/etc/nginx`로 하면 에러가 발생한다.  
`/etc/nginx/nginx.conf` 로 하던가 `/etc/nginx/` 이렇게 `/`로 끝나도록 설정하면 해당 디렉토리로 copy를 해준다.