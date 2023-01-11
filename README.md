# Ya_requests
from pprint import pprint
import os
import requests
import json

class YaUploader:
    files_url = 'https://disk.yandex.ru/d/kE5fQzhrgYtKVA'
    upload_link = 'https://cloud-api.yandex.net/v1/disk/resources/upload'

    def __init__(self, token: str):
        self.token = token

    @property
    def headers(self):
        return {
            'Content-Type': 'application/json',
            'Authorization': f"OAuth {token}"
                  }

    def get_upload_link(self, file_path):
        filename = file_path.split('/', )[-1]
        params = {'path': file_path, 'overwrite': 'true'}
        response = requests.get(self.upload_link, params=params, headers=self.headers)
        pprint(response.json())
        return response.json()

    def upload(self, file_path ):
        href = self.get_upload_link(file_path).get('href')
        if not href:
            return

        with open(file_path, 'rb') as file:
            response = requests.put(href, data=file)
            if response.status_code == 201:
                print('File uploaded successfully')
                return True
            else:
                print('File upload failed', response.status_code)

if __name__ == '__main__':
    filename = 'my_file.txt'
    token = ''
    uploader = YaUploader(token)
    result = uploader.upload(filename)
    print(result)
