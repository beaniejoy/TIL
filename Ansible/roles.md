# roles 적용

## files

```
roles
├── package
│   ├── files
│   │   └── test.txt
│   └── main.yml
└── ...
```
`files`에 지정한 file 들은 main.yml에서 copy할 때 사용가능
```
---
- name: Copy Test
  ansible.builtin.copy:
    src: test.txt
    dest: "{{ remote_dir }}/test.txt"
    owner: "{{ remote_server_user }}"
    group: "{{ remote_server_group }}"
    mode: 0644
```
이런 식으로 src에 파일 이름만 설정하면 해당 role의 `files`을 참조한다.